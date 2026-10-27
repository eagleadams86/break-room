# Break Room — rules for Claude sessions

A room a distributed team drops into beside the Teams or Zoom call it already has open: a
nickname and a six-letter code to join, a roster with online dots, a host who runs short
games — trivia first — with a running score for the night. For BOTH Charles's M&T work team
and a friends group, 7–12 people a room. Deployed via GitHub Pages:
https://eagleadams86.github.io/break-room/ — dev port **8026**.

Built from the family starter (`~/claude-starter`) on 2026-10-27. Everything the starter
carries — the CSP, the Auto theme default, the `data-booting` gate, the header row and its
scroll arrows, the help window and info dot, Back Up and the delete-all window, the
service worker, the SCHEMA halt and the test harness — is the family's, and the comments
beside each block say so. **This file records only what Break Room decides that the family
has not.** The full plan is at `~/.claude/plans/there-used-to-be-flickering-lovelace.md`;
memory note `break-room-project` summarises the decisions.

## What is new here, and why

- **Everyone signs in ANONYMOUSLY, and that is a deliberate divergence from League Night's
  "Anonymous must stay off".** There, any signed-in account could read any league by id, so an
  account had to cost something. Here a room is MEANT to be readable by anyone holding its
  code, nothing in it outlives the night, and an M&T colleague must never need a Google
  account — the corporate filter blocks `firebaseapp.com`, and anonymous sign-in uses only
  `identitytoolkit.googleapis.com`, which League Night proved reaches M&T. Consequences,
  argued in `firestore.rules` and the README: `signedIn()` is FREE and never sufficient on its
  own; `allow list: if false` on `rooms` is the WHOLE read boundary; anyone with the public
  config can create rooms, bounded by the caps, the twelve-hour `expiresAt` ceiling, the TTL
  policies, a budget alert, the API key's referrer restriction (a speed bump, not a boundary)
  and the one-click kill switch of turning the Anonymous provider off. Nothing identifies a
  person across nights, which is the privacy property v1 wants. Google sign-in (M6) is
  OPTIONAL and only for a persistent "house"; a room never needs it.
- **The host's browser is the engine, and the only writer of the room document.** Every
  write is `runTransaction` with `rev + 1`, which the rules refuse otherwise. Players write
  only their own two documents (presence, answer). `step()` on each game is PURE — `(room,
  {answers, players, now}) → patch | null` — so the suite drives whole games with fake
  answers and a fake clock, and a host reload rebuilds from the room document because nothing
  lives only in host memory. The host may HAND the room to any present player (an addition to
  the plan's sketch, so Leave as host does not cost the room a three-minute wait); a player
  may TAKE it only when the old host's `seenAt` is three minutes stale, and only `host` and
  `rev` may change in that write.
- **Timers are wall-clock, never counters** (memory: hidden-tab timers are unreliable). The
  host writes `stepAt: serverTimestamp()` + `stepMs`; each client learns its clock offset
  from its own heartbeat (`seenAt: serverTimestamp()` read back once `hasPendingWrites` is
  false). Scoring never reads a client clock — speed bonuses are `answer.at − stepAt`, both
  server timestamps, and the rules prove both are sentinels (`== request.time`).
- **Presence is a 90-second heartbeat; online is four minutes; stale is three.** The two
  are deliberately unequal: a host must be stale BEFORE the roster calls them offline, or the
  roster would show a host nobody can replace. A test pins the inequality.
- **TWO TABS IN ONE BROWSER PROFILE ARE ONE PLAYER.** The anonymous uid lives in IndexedDB
  per origin, so a second tab is the same uid, the same player document, the same answer.
  Multi-player testing needs a second Chrome profile or an incognito window per extra
  player. The README says so too.
- **`room`, `players` and `answers` are memory only.** Never saved locally, never in a
  backup, never in a share link. `localStorage['break-room-local']` holds `{nick, code,
  packs, recent}` and nothing a room contains; `break-room-theme` is the head script's.
  Every snapshot comes through `normalizeRoom` / `normalizePlayers` / `normalizeAnswers`,
  which REBUILD rather than merge: a uid failing `ID_RE` is DROPPED with its record (never
  repaired — a repaired uid is a player nobody is), strings are sliced to their caps, lists
  capped, Timestamps converted to ms by `toMs()`. Every cap mirrors a line in
  `firestore.rules` and the suite reads both files to check the pairs.
- **A room written by a newer build halts THIS page.** `haltForNewerData` has a second job
  the starter's never had: `room.schema > SCHEMA` stops the page, because an old host's next
  transaction would rewrite that room without the fields the new build added — for everyone
  in it. `window.brHalted` is what the module and the worker block read (the starter's
  `viewOnly` flag, which this app has no use for otherwise).
- **No tabs, no Find, no chart, no entries table, no Settings window, no share codec.** The
  screens are PHASES (home → lobby → play → results), decided by `render()` from
  `room ? room.phase : 'home'`, not tabs — there is nothing to switch between inside a room.
  Nothing to find, nothing to plot, no figures to export. The starter's Share window became
  the **Invite** window (a `#join=CODE` link, Web Share, copy) in the same header slot with
  the same ↗ glyph. **Don't "restore" any of them**: `EXPECTED` dropped from the starter's
  153 to 94 at M0 for exactly this, and the suite's groups say which family tests were kept.
  The starter's row-measurement frames (360/500/1600px) went too — this header has two
  controls and never overflows, so "sanity: the controls overflow here" could not hold; the
  arrows stay wired and pinned by source.
- **Trivia packs ride on the ROOM, not in links.** Four packs of 40 are embedded in the
  page (`PACKS`); a custom pack the host wrote lives in `local.packs` and is COPIED onto
  `room.pack` when the game starts, so players and a handed-off host have it. Back Up covers
  the custom packs; Delete All ("Forget This Browser") clears nick, code, recent and packs.
- **The dot is `--ok` or `--text-muted`, never red for away.** Charles is red-green
  colourblind, and "away" is an absence, not a warning; a visually-hidden word beside the
  dot carries the state for a screen reader. Avatars are the pack's `--series-1..5` ramp,
  tint fill on a full-strength edge (pack rule 3), chosen by `hue % 5`.
- **Dev port 8026**, recorded in `.claude/launch.json` AND `~/.claude/launch.json`. 8021 is
  not free; 8026 is the first past Wine Cellar's 8025.

## Status

**M6 — Google sign-in and houses (2026-10-27, branch `m6-houses`).** The family's GIS flow
(League Night's `SIGNIN_DOORS` = `syncBtn` + `houseSignInBtn`, no `await` on the warm path,
`popupIsAffordable`), `#syncBtn` FIRST in the row at a fixed `8em` with an ellipsis,
`users/{uid}` (self only, `{nick, houses:[{id,name}]}`, every write a merging transaction)
and `houses/{code}` (eight characters; `{owner, rev, schema, name, createdAt, members, room,
roomExp, board, last}`), the Houses card on the home screen, the house window (code, link,
leaderboard, members, Leave / Delete), `#house=` arrival, the room card's house line, and a
demo house (`DEMXTEAM`) so the board is in the sample data. **Rules change** (users, houses,
`google()` from the token's identities, `house` on `validRoom`) — must be re-pasted.
**SCHEMA 5** (a room's `house`). CSP = League Night's line exactly. `EXPECTED` 228 (was 227; +1 for the stale-room fix
below). **Tonight's room can go stale (fixed 2026-10-27):** the last player out deletes the
room doc, but the house kept `room`/`roomExp`, so the card said "tonight's room is open" and
Join hit "has ended". Now `leaveRoom` clears the house (`closeHouseRoom`, members only, only
if the house still names THAT code) and `openHouseRoom` checks the room with `roomAlive()`
first, replacing a dead one. No rules change (clause 1 lets a member set `room: null`).
The console step (README step 9) is done and `GOOGLE_CLIENT_ID` is set. Observed against the real
cloud from Charles's Chrome plus a Playwright guest: an anonymous host signed in and kept
the SAME uid and the host's seat; a reload kept the Google session; a house created and
listed from `users/{uid}`; tonight's room opened from it; the guest refused (`permission-
denied`) reading the house, writing itself into `members`, writing its own users doc and
creating a house; the board counted 150 then only the difference to 400, one night, the
guest left off; sign-out left as the account and rejoined as a new anonymous player with
the code kept; signing in again from that anonymous player hit `credential-already-in-use`,
switched back to the SAME Google uid, rejoined the room with the board intact, and took the
seat back through the handoff clause ~30 s later. NOT observed: a second Google account
joining by `#house=` link (only one account to test with).

**Things a tidy-up would break (M6):**
- **Sign-in LINKS (`linkWithCredential`) onto the anonymous uid, never signs in fresh.** The
  seat, tonight's scores and the house membership must be one uid; a fresh sign-in would
  make the player a stranger in their own room. `onAuthStateChanged` does not fire on a
  link, so `onAccount()` is called by hand after `getIdToken(true)` (the rules read the new
  identities off the token). Only `credential-already-in-use` switches uid, and it calls
  `brApp.beforeSwitch` FIRST: `leaveRoom(null, {switching: true})` hands the seat on if it
  can, NEVER closes the room, keeps `local.code`, and `afterAuth` rejoins as the new uid (a
  host alone: its player record goes, so the rules call it stale and the rejoin takes the
  seat at once). Sign-out is the same switch the other way.
- **`google()` in the rules asks `token.firebase.identities`, not `sign_in_provider`** — a
  linked account's identities name google.com whichever way it last signed in.
- **The house tally is idempotent by construction** (`houseTally`, pure): `last` is what
  has been counted from `last.code`, and only the difference is added. Any member's browser
  may run it, concurrently — `houseRecorder` picks one for tidiness, not correctness. Don't
  replace it with "add this game's points": a retried transaction would count twice.
- **`TALLY_CODE_RE` is a shade wider than `CODE_RE`** because the demo room's code starts
  "DEMO" (an O no real code has). Tighten it and the demo house counts every snapshot again.
- **`house` goes on a room only when there is one** (`if (house) doc.house = house`), so a
  plain room is valid under the pre-M6 rules — that is what let the client ship before the
  paste.
- **The house sign-in button and both forms are MARKUP**, not innerHTML: the module warms
  doors by id at load, and a redrawn button would be a cold door (the iPhone dead tab).
- **No member removal**, on purpose (League Night's lesson): anyone with the code rejoins.

**M5 — Codenames (2026-10-27).** `GAMES.codenames` — teams **Blue and Gold** (`'b'`/`'g'`,
the pack's `--series-1`/`--series-2`; never red/green, and every coloured card names its
colour in a `.tag`), a `teams` step the host can Shuffle and Start, then `clue` (a
spymaster's `C|word|n` answer) → `guess` (an operative's card index, or `'pass'` after one
guess) per turn, 9/8/7/1 cards, the 504-word `CN_WORDS`, 100 to everyone on the winning
team. The key (`{n, c: [25 colours 1–4]}`) lives in **`live/key`**, readable by `game.spies`
= `cnSpies(host, spy)` = [host, Blue's spymaster, Gold's spymaster] — the rules had that
clause from M0, so **no rules change**. `normalizeKey` reads both shapes now. **SCHEMA 4.**
Observed against the real cloud with four Playwright contexts: the key read by the host and
both spymasters and refused to the operatives (`permission-denied`), the host leaving
mid-game, every turned card matching the key, the whole board on the room at results and
the key emptied, zero console errors. `EXPECTED` 202.

**Things a tidy-up would break (M5):**
- **Every turned card moves `round`.** An operative's next press is a new round, so it can
  never be read as the last one; when two teammates press in the same round the EARLIER
  `at` (server time) wins and the other press is simply never read. The turn's clock
  (`stepAt`) does NOT restart on a turned card — only on a new clue or a new turn.
- **The host is in `spies`, so a host who is an operative COULD read the key** with the
  developer tools — its browser turns the cards. The honest screen shows the key only when
  `who` is a spymaster (`body()`), and a test walks the host-operative's board. Same bar as
  the drawing word; don't "fix" it with a server.
- **`spies` is recomputed, not appended**: step() returns `{spies: cnSpies(me, spy)}`
  whenever the list differs, which names a new host AND strikes off the old one (who may
  come back as an operative). Promoting a spymaster rewrites it in the same write.
- **People leaving is handled in step(), in this order**: name the host → seat late joiners
  on the smaller team → replace an absent spymaster → move an operative across when a team
  is down to one and the other has three operatives → skip the turn of a team with no
  operative → end the game (`why: 'short'`, no winner) when neither has one. Found by the
  first real-cloud run: a team left with only its spymaster ran out three-minute clocks
  for ever. The last check must stay BEFORE the turn skip, or two spymasters alone would
  hand the turn back and forth on every tick.
- **The room carries only `revealed` (turned cards) until results**, when `colors` (the
  whole key) goes on and `_key` empties the document. `cnLeft()` works from `revealed` and
  `first` alone, never the key, so the "8 left" pill is safe on every screen.
- **`words` is 25 or absent** — the rules check `words.size() == 25` — so `normalize`
  turns a short board into `[]` rather than padding it.
- **The demo's clue words must not clash with any `CN_WORDS` entry** (a test checks the
  whole list): "sport" and "holiday" did ("port", "day").
- **The clue form refreshes in place** (`GAMES.codenames.refresh`, keyed on step, round,
  who, sent, key-present, teams, spymasters, host), and `cnDraftN` keeps the number across
  a full redraw. On a phone (≤ 560px) the tags shorten (`.cn-s`: "Byst.", "Assn."); the
  long form stays in the aria-label.

**M4 — icebreakers (2026-10-27).** `GAMES.ice` — one game, three `game.mode`s: `whoSaid`
(write → a 15 s `guess` per card → `reveal`), `twoTruths` (write three, lie first → a 20 s
`lie` per player → `reveal`) and `prompt` (write → one 30 s `vote` over every card →
`reveal`), 24 embedded prompts each for the two prompted modes, 50 / 25 / 100 points, no
speed bonus. The author map (`{n: nonce, a: [author per card], l: [lie per card]}`) lives
in **`live/key`**, readable only by `game.spies` = `[host]` — the rules had that clause
from M0, so **no rules change**. `normalizeKey`, `normalizeIceCards`, `iceSubmission`, the
module's `syncKeyListener` and `writeKey`, `brApp.onKey`, the demo's writers and voters.
**SCHEMA 3.** Observed against the real cloud with three Playwright contexts: the key
refused to both non-hosts (`permission-denied`), the host pressing Leave mid-guess, the
new host naming itself in `spies` and finishing the prompt with every card credited to its
true writer, the cards and the key emptied at results, zero console errors. `EXPECTED` 184.

**Things a tidy-up would break (M4):**
- **The key is written BEFORE the room**, from the patch's `_key` in `pushPatch`, and the
  two share `game.nonce`. A step() that finds a key with another nonce (or none) WAITS —
  for `ICE_KEY_WAIT_MS` past the step's time, then gives the prompt up with a feed line —
  rather than scoring cards against last deal's map. `_key` must never reach `push()`.
- **A host that is not in `game.spies` returns `{spies: [me]}` before anything else.**
  The handoff clause in the rules lets a claim change only `host` and `rev`, so a new host
  cannot name itself in the same write; step() does it on its first tick. That needs
  `ctx.me`, which is why hostTick passes `me` and `key` (a test pins the call).
- **The host adopts its own key on write** (`iceKey = normalizeKey(...)` after
  `writeKey`): a listener never re-fires for its own write once it is committed. The key
  listener has NO `hasPendingWrites` skip for the same reason.
- **Cards keep their positions through the boundary** — a junk card normalizes to an
  empty one, never dropped — because the key names authors BY POSITION. `normalizeKey` is
  all or nothing for the same reason.
- **Authors are listed in ARRIVAL order (`game.authors`), never card order**, and nothing
  on the room names a writer before `reveal`: a test walks the host's and a player's
  screen during a guess. "This one is yours" comes from `myIce.write`, this browser's
  memory, and the host ignores a pick on your own card whatever the screen showed.
- **The ice stage refreshes in place** (`GAMES.ice.refresh`, keyed on mode, step, round,
  WHO and "answered"): the two-truths form is three boxes and `keepTyping()` only keeps
  the focused one. `who` is in the key because the suite plants a second viewer on the
  same step — without it the first viewer's buttons stayed on screen.
- **Results empty the cards and the key** (and a new prompt empties the cards): what
  people wrote leaves the cloud with the prompt it answered. privacy.html says so.

**M3 — drawing & guess (2026-10-27, branch `m3-drawing`).** `GAMES.draw` — a PURE `step()`
through `pick` (10 s) → `draw` (60/75/90 s, hints at 40% and 67%) → `turnEnd` (6 s) → the
next ONLINE drawer, `advance()` appending late joiners — a synchronous `sha256Hex`, the
563-word `DRAW_WORDS`, `choicesFor` / `resolveWord`, the pick (`P|hash|shape|hints`) and
guess (`G|n|text`) answer encodings, the stroke codec (`n:c:w:x,y …`, `SEG_RE`), the
`makeCoalescer` (400 ms on an injected clock), `normalizeStrokes`, the canvas (`pad`), the
module's `live/strokes` listener and two writes (`strokes` = merge + `arrayUnion`,
`clearStrokes` = set), the lobby's read estimate (`drawCost`), and the demo's doodlers.
**SCHEMA 2**: a room written by this build halts an M2 page rather than letting it host a
game it cannot run. **No rules change** — `live/strokes` was in the rules from M0, and the
pick and guesses fit the answer document's 120-character string. Observed against the real
cloud with three Playwright profiles: first stroke on the watcher 0.3 s after pen-down, the
guessing host never shown the word, a mid-turn joiner given the whole drawing in one read,
a right guess shown to a third player only as "got it", zero rule refusals. `EXPECTED` 163.

**The hash hides the word from the honest screen, not from a cheat.** The host's browser
has the seed, the round, the drawer's uid and the word list — `resolveWord` is the same
brute force a curious host could run in the console. That is the plan's stated bar for a
party game; don't "fix" it with a server, and don't let anything render the word before
`turnEnd`.

**Things a tidy-up would break (M3):**
- **The canvas is ONE element for the visit, MOVED into each view's `#drawSlot`**, and the
  drawing card REFRESHES IN PLACE (`GAMES.draw.refresh`, keyed on step/round/role/got)
  between steps. Rebuilding either mid-stroke drops the pointer and ends the line; a test
  pins that `#drawStage` survives a snapshot. `keepTyping()` is the second layer, for the
  guess box across a full redraw — not a reason to drop the first.
- **`step()` must stay synchronous**, which is why SHA-256 is hand-written rather than
  `crypto.subtle` (async). The suite checks it against known vectors and against
  `crypto.subtle` on a long UTF-8 string.
- **Every run of whole words in a guess is hashed** (`guessMatches`), not only the whole
  guess. Otherwise "is it a cat?" is WRONG — and a wrong guess goes into the feed for
  everyone, word and all.
- **A guess carries a counter (`G|n|`) and the room keeps `game.seen`**: the answer document
  is overwritten guess by guess, and without the counter `step()` would re-read the same
  guess every tick. A reloaded guesser resumes from `seen[me] + 1`.
- **`seq` on the strokes document is the ROUND it belongs to**, and a watcher ignores a
  document whose `seq` is not this turn's — that is what stops last turn's drawing showing
  under a new word if a clear is slow. The drawer clears BEFORE sending its pick (one
  browser's writes land in order).
- **The segment counter `n` never restarts within a turn**, Clear included
  (`co.reset(co.next())`): `arrayUnion` silently drops a string already in the list, so two
  identical strokes must be two different strings.
- **Never name a draw-game field `words` or `cards`** — `validRoom` size-checks those two for
  the later games, and a drawing room would be refused. A test pins it.
- **`brApp.onAuth` does nothing while the demo is on**, and `leaveRoom` picks the cloud uid
  up afterwards: the anonymous sign-in can finish after the demo room opens, and used to
  take the demo host's seat away (found in M3; it was there since M1).
- **The host's answers listener is dropped BEFORE a Leave hands the seat on** — the rules
  refuse it the moment the handoff lands, and it said so in the console.
- **The last drawer's browser wipes the drawing on the step into results** (`onRoom`):
  the room still names it as `drawer` there, which is what the strokes rule needs — after
  Back to the Lobby nobody could. Without it a game's last picture stayed in the cloud.
- **Nothing may promise a room is "deleted within a day"** — on Spark there is no TTL, so a
  room everyone closed the tab on stays stored (unreadable: the rules refuse an expired
  room). privacy.html's "When It Is Deleted" is the honest list; a test bans the old phrase.
- **Demo nicknames are ≤ 20 characters** — `NICK_MAX` slices, and two of them used to lose
  their closing bracket. The demo's pace is read off "quick" / "slow" in the nickname.

**M2 — trivia (2026-10-27).** `GAMES.trivia` — `normalize`, `options`, `setup`, a PURE
`step()`, `encode`, `view`, `tick`, `onClick`, `results`, `demoAnswer` — four embedded packs
of forty (`PACK_LIST` → `PACKS`), `parsePack` and the Custom Packs window, the countdown
from server time plus the measured offset. The suite drives a whole game through `step()`
with fake answers and a fake clock, pins the packs (forty each, four distinct answers, no
repeats, unchanged by the boundary) and the parser's line-numbered report. The demo's
invented players answer on their own, each at a pace named in their nickname (since M3,
"quick" and "slow"). `EXPECTED`
131.

**The cloud went on the same afternoon (project `breakroom-3722e`, Spark plan).** Observed
in two real browsers (the desktop app's pane and Charles's Chrome, two anonymous uids): A
creates, B joins by the `#join=` link with the fragment stripped and the code in the box,
both see each other within a second; a ten-question game with answers from both sides,
scored 133 and 111 by server time; the host reloads mid-game and rejoins as host with the
game still moving (rev went on without a gap); the host's tab closed for three minutes, and B's
browser claimed the seat through the handoff clause and ran the game to Results. The hidden-tab
countdown is pinned by a test and not yet watched on a real backgrounded tab. **Not on Spark: the four TTL policies and
the budget alert** — both need Blaze. The README's checklist says what that costs (abandoned
rooms are not deleted; everything else is). Two paste lessons: `firestore.rules` is
ASCII-only because the console editor mangled UTF-8 punctuation; and every answer button
carries an `aria-label`, because a button whose only text is in two spans was unnamed to
the accessibility tree the browser tools read.

**Things a tidy-up would break (M2):**
- **`myPick` is reset when the phase leaves `play` or the kind changes**, in `onRoom`. A
  new game starts its rounds from 1, so an answer remembered from the last game would read
  as this game's and close the buttons before anyone pressed one.
- **`step()` waits for everyone ONLINE, never everyone on the roster** — a player who shut
  the laptop would otherwise hold every question to its full time. And it reads only answers
  whose `r` is the current round: last round's answer document is still sitting there.
- **The speed bonus is `floor(100 × remaining / stepMs)` from `answer.at − stepAt`**, both
  server timestamps, clamped to [0, stepMs]. A wrong answer is not a key in `gained` or
  `scores` at all — a nought there would read as "answered wrongly" on the board.
- **A custom pack is COPIED onto `room.pack` at setup**; the embedded ones travel as an id.
  `questionAt` reads whichever applies and answers null rather than throwing, and the view
  says "missing" — a reload on a build that lost a pack id must not take the room down.
- **`PACK_LIST` is declared ABOVE `GAMES.trivia`** (which reads `PACKS` in `options()` at
  call time, but `PACKS` itself is a top-level literal) — a `const` is in its temporal dead
  zone until its line runs, League Night's boot-time TDZ.
- **The reveal re-asserts `opacity: 1` on the disabled answers** (`.answers.revealed
  button:disabled`): pack rule 20 dims a disabled control, which is right for a control and
  wrong for a result the reader is meant to study.

**M1 — the lobby (2026-10-27).** The cloud module (anonymous sign-in, listeners with the
`hasPendingWrites` skip, `create`/`join`/`push`/`answer`/`heartbeat`/`claimHost`/`extend`/
`leave`), the lobby with roster and dots, the room card with the expiry countdown and
Extend, the Invite window and `#join=` arrival (lenient, fragment stripped at once), the
host loop (`hostTick`: one push at a time, waits for its own rev), the handoff, and the demo
transport (`demoSim`) with four invented players. `FIREBASE_CONFIG` is still empty: nothing
cloud-side has been exercised against a real project yet — the module is argued from
League Night's and the rules, not observed. `EXPECTED` 111.

**M0 — scaffold (2026-10-27).** The shell, the local store, the room-code helpers, the
boundary allowlists, the presence and handoff helpers, the home card, `firestore.rules`
argued clause by clause, privacy.html for anonymous sign-in and six-hour rooms. The
Firebase console steps are a numbered checklist in the README and need Charles.

**Things a tidy-up would break (M1):**
- **The demo transport writes nothing to storage** — `enterRoom` skips `local.code` and
  the recent list while `demoOn`, or a reload would try to rejoin a room that only ever
  existed in one tab's memory. The suite pins that the demo never reaches `recent`.
- **`renderScores` draws nothing until a score exists.** A table of noughts before the
  first game teaches nothing and the roster already names everyone.
- **The host's picker survives a redraw because `pickState()` reads it back first.** The
  room is redrawn wholesale on every snapshot (a diff would be a second renderer to keep
  right); the picker is selects, never a text box, for the same reason.
- **The suite moves the frame's clock to the fixtures' T0** (`__setOffset`) before
  reading a dot. T0 is in 2027; without the offset every fixture player reads as online.
- **A join link's code is read with a boundary on BOTH sides** — `(?:^|[^A-Z0-9])` before
  and `(?![A-Z0-9])` after — or `#join=ABCDEFG` reads as `BCDEFG`.

## Editing rules

- `theme.css` is a byte-copy of the pack's. Never edit it here; when the pack moves, copy
  the fresh file in. The pack's `check_consumers.py` lists this repo.
- The suite pins `EXPECTED` in `tests.html` — bump it when adding a test; removing one
  fails the build on purpose. Tests refuse to run off localhost. Run them headless with
  Playwright (the Browser pane throttles a hidden tab's timers); see `running-the-suites`
  in memory.
- A new field on a room, a player, an answer or a house goes into the matching `normalize*`
  allowlist, into `firestore.rules`, AND bumps `SCHEMA` in the same commit, or the boundary
  strips it and the rules refuse it.
- Nicknames (20), house names (30), questions (160), answers (60 × 4), drawing guesses (40), icebreaker
  answers (120, or 60 × 3 for two truths) and Codenames clues (one word, 24) are the only free text, capped in code; guesses and answers live only for the round; **there is no notes field anywhere, and
  none is to be added.**
- Escape everything rendered with `esc()`. Every nickname, question and answer on screen
  was typed in another browser: it is untrusted input on the shared origin.
- Keep README.md current in the same commit as the change it describes.
- **No GitHub Releases for this app** (Charles, 2026-10-27) — unlike the family's
  single-file apps. A downloaded copy cannot host a room (the cloud answers only the live
  site and `localhost:8026`), so a release zip is waste. The 2026-10-27 release and tag were
  deleted. A milestone ships when it is merged and the Pages deploy is confirmed.
