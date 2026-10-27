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
  person across nights, which is the privacy property v1 wants. Google sign-in is the LAST
  milestone, for a persistent "house".
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

**M2 — trivia (2026-10-27).** `GAMES.trivia` — `normalize`, `options`, `setup`, a PURE
`step()`, `encode`, `view`, `tick`, `onClick`, `results`, `demoAnswer` — four embedded packs
of forty (`PACK_LIST` → `PACKS`), `parsePack` and the Custom Packs window, the countdown
from server time plus the measured offset. The suite drives a whole game through `step()`
with fake answers and a fake clock, pins the packs (forty each, four distinct answers, no
repeats, unchanged by the boundary) and the parser's line-numbered report. The demo's
invented players answer on their own, each at a pace named in their nickname. `EXPECTED`
131. Still unobserved against a real Firebase project: everything in the module, and the
three "Done" lines in the plan that need two browser profiles (A creates / B joins / C by
link; host reload mid-game; hidden-tab countdown on return) wait for the console steps.

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
transport (`demoSim`) with four invented players. `FIREBASE_CONFIG` is still null: nothing
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
- A new field on a room, a player or an answer goes into the matching `normalize*`
  allowlist, into `firestore.rules`, AND bumps `SCHEMA` in the same commit, or the boundary
  strips it and the rules refuse it.
- Nicknames (20), questions (160) and answers (60 × 4) are the only free text, capped in
  code; guesses and answers live only for the round; **there is no notes field anywhere, and
  none is to be added.**
- Escape everything rendered with `esc()`. Every nickname, question and answer on screen
  was typed in another browser: it is untrusted input on the shared origin.
- Keep README.md current in the same commit as the change it describes.
