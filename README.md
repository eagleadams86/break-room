# Break Room

A room your team drops into beside the Teams or Zoom call you already have open. Pick a
nickname, start a room or join one with its six-letter code, see who's around, and let the
host run a round of trivia, a drawing game, an icebreaker or Codenames while everyone talks. No account, nothing to install, and
nobody can open a room once it has ended. Groups that meet again can sign in with Google
for a **house**: a standing group with tonight's room one press away and a leaderboard
across nights.

**Live: https://eagleadams86.github.io/break-room/** · **Privacy:** [privacy.html](https://eagleadams86.github.io/break-room/privacy.html)

Built for two groups at once — a distributed work team on corporate laptops and a friends
group — so a room never needs a Google account (only a house does), never opens a video stream, and never asks for
anything from a work system. The plan is in
`~/.claude/plans/there-used-to-be-flickering-lovelace.md`; `CLAUDE.md` records what this app
decides that the rest of the family has not.

## What It Does Today

**Milestone 6 — Google sign-in and houses (27 October 2026).** Optional, and only for a
group that meets again:

- **☁️ Sign In** at the left of the header (or *Sign In With Google* on the Houses card)
  opens Google's own sign-in window straight onto `accounts.google.com` — never
  `firebaseapp.com`, which the corporate filter blocks. Google is **linked onto the
  anonymous player you already are**, so your seat in the room and tonight's points stay
  put. Only an account that already exists from another browser changes who you are, and
  then the app leaves the room and comes straight back in as that account.
- **Start a House** with a name, or **Join a House** with its **eight-letter code** (a
  room's is six, so the two are never confused) or its invite link
  (`…/break-room/#house=CODE`). Your houses follow your account to any browser.
- **Open Tonight's Room** from a house: a normal room, labelled with the house. Every other
  member sees *Join Tonight's Room* on their Houses card. Two members pressing at once get
  one room, not two. When the last player leaves, the room closes and the house forgets
  it; if a house still names a room that has ended, pressing the button opens a fresh one
  instead of a dead end. Guests still join with the six-letter code and no account.
- **The leaderboard.** Every point a **member** scores in a house's room is added to their
  row — total points, nights scored, best night — counted once however many members are in
  the room (the tally only ever adds the difference since it last counted). Guests are not
  on it. Leave a house and your row stays, marked "left". The founder deletes the house from
  the same window: press **Leaderboard**, then **Delete the House** at the bottom.
- **The demo room belongs to a demo house**, so the leaderboard can be seen with no account:
  the invented players have a few nights behind them and your points join them.
- Signing out leaves the room as your account and rejoins it as a guest.

**Milestone 5 — Codenames (27 October 2026).** The team word game, played beside the call:

- **Two teams, Blue and Gold** — never red and green, which a red-green colourblind player
  cannot tell apart — and every coloured card also says its colour in words. The host
  picks Codenames in the lobby, with how long a spymaster has for a clue and a team has to
  guess (2, 3 or 5 minutes each); it needs **four people online**.
- **The host sees the teams first** — everyone online split as evenly as it goes, one
  spymaster each — and can **Shuffle Teams** until it feels right, then **Start**. The team
  that starts has nine words to find, the other eight; seven are bystanders and one is the
  **assassin**.
- **The spymaster types one word and a number** ("ocean 2"). A clue that is a word on the
  board, or inside one, or around one, is refused. The team talks it over **on the call**
  and anyone on it presses a word: their own colour lets them carry on (up to one more than
  the number), anything else ends the turn, and the assassin loses the game on the spot.
  When two teammates press at once, the earlier press counts. **End Our Turn** appears
  after the first guess.
- **Points:** everyone on the winning team, spymaster included, scores **100** toward the
  night's total.
- **Only the host and the two spymasters can read the key.** Which word is whose lives in
  the same private record the icebreakers use, and the security rules refuse it to every
  other player. The host's browser has to read it to turn cards over, so a host who is an
  operative could look with the developer tools — like the drawing word, the key is kept
  off the honest screen, not away from a determined cheat. The room itself only ever holds
  the cards already turned; at the end the whole board is shown to everyone and the key
  record is emptied.
- **People come and go.** Someone who joins mid-game goes onto the smaller team; a spymaster
  who has gone is replaced by a teammate who is here; a team down to one while the other has
  three or more gets a player moved across; a team with nobody left to guess has its turns
  skipped; and when neither side can guess, the game stops with no winner.
- **The demo room plays it**: the invented spymasters give clues, and the invented
  operatives peek at the key (the demo's privilege) and are right three times in four. On
  your own team they wait longer, so you get the first press.

**Milestone 4 — icebreakers (27 October 2026).** Three ways to get people talking, picked
under *Kind* once the host chooses Icebreakers in the lobby:

- **Who Said It?** Everyone finishes a sentence about themselves ("My most useless
  talent…", one of 24 prompts; the host picks 1–3 prompts a game). The answers go up one at
  a time, **unsigned and shuffled**, and everyone has 15 seconds to guess who wrote each.
  Your own answer says "This one is yours — keep a straight face" instead of offering you
  the buttons. Needs three people online.
- **Two Truths and a Lie.** Everyone fills in three boxes — the lie, a truth, another
  truth — and the three are shuffled before anyone sees them. Then one player's set at a
  time, everyone else has 20 seconds to pick the lie.
- **Best Answer.** A silly prompt ("A terrible name for a boat"), every answer on screen at
  once, unsigned, and 30 seconds to vote for a favorite. You cannot vote for your own.
- **Points:** 50 for guessing the writer or spotting the lie, and **25 to the writer for
  every player they fooled**; in Best Answer, 100 for every vote your answer gets. No speed
  bonus in any of them — this is the talking game. Points join the night's running total
  like the others.
- **Nobody's screen names a writer until the reveal — not even the host's.** Which answer
  is whose (and where the lie went) is kept in a separate document in the cloud that the
  security rules let **only the host's browser** read; the room everyone reads holds only
  the shuffled text. If the host leaves or goes quiet mid-game, the player who takes over
  names themselves as that document's reader and the game carries on from the same answer.
  As with the drawing game's word, a host with the browser's developer tools open could
  find out early: it hides the writers from the screen, not from a determined cheat.
- **What people wrote leaves the cloud with its prompt.** The next prompt clears the
  answers off the room, and the end of the game clears them and the who-wrote-what map.
- **The demo room plays all three**: the invented players write canned answers, guess,
  spot lies and vote at their own pace.

**Milestone 3 — drawing & guess (27 October 2026).** One person draws, everyone else
types guesses:

- **The host picks Drawing & Guess** in the lobby, how many rounds (everyone draws once a
  round) and how long a drawing lasts (60, 75 or 90 seconds). Beside the choice is an
  estimate of what the night costs in cloud reads against the free plan's 50,000 a day, and
  it turns amber when one evening could use most of it.
- **The drawer is offered three words** from a list of 563 and has ten seconds to choose (the
  browser chooses the first if they don't). Then a canvas, six ink colours from the theme,
  an eraser, four line widths and Clear. Everyone else sees the drawing appear about a third
  of a second behind the pen, the word as blanks, and a letter given away at 40% and 67% of
  the time.
- **Guesses go into a list everyone can see** — except a right one, which only ever shows as
  "Ben got it". A guess is checked on letters only ("Ice-cream!" is "ice cream"), and a right
  word inside a sentence counts ("is it a cat?"), so a right answer can never land in the
  list for everyone to read.
- **Points:** 100 plus up to 100 for speed for each player who gets it, timed by the
  server's clock, and 50 to the drawer for every one of them. The turn ends when everyone
  online has it, or at the buzzer; six seconds show the word and what it paid; then the
  next person draws. Someone who joins mid-game draws at the end of the round.
- **The host is a guesser too, and never sees the word.** The drawer's browser sends a
  one-way fingerprint of the word (SHA-256), not the word, and the host's browser checks
  each guess against it. The word reaches the room only at the end of the turn. A host who
  opened the browser's developer tools could work it out — this hides the word from the
  screen, not from a determined cheat, which is the right bar for a party game.
- **A late joiner sees the whole drawing at once**: every stroke of a turn lives in one
  small document, so arriving is one read.
- **The demo room draws too:** the invented players take turns doodling (a sun, a house, a
  heart…) and guessing at their own pace, so the whole game can be watched alone.

Also fixed on the way: in the demo room, the cloud's sign-in finishing a moment after the
room opened took the host's seat away from you; two demo nicknames lost their closing
bracket to the 20-character cap; and an avatar read "P(" for "Priya (online)".

**Milestone 2 — trivia (27 October 2026).** The first game, and the shape the next three
follow:

- **Four packs of forty**, embedded in the page: General Knowledge, Office & Tech, Movies &
  Music, and 80s to 2000s. The host picks a pack, how many questions (10, 20 or 40) and how
  long each one gets (15, 20 or 30 seconds), and presses *Start Trivia*.
- **A question at a time.** Four big answers, a countdown, and "3 of 4 answered" so the
  host can see who is still thinking. The question closes when everyone online has answered
  or the time is up, whichever is first; the reveal then holds for five seconds — the right
  answer marked, your wrong one marked, the room's spread under each option and your points
  for the question — and the next one opens.
- **Points.** 100 for a correct answer plus a speed bonus of up to 100 for how much of the
  time was left, measured by the server's clock on both ends. Points carry across games for
  the whole night; a late joiner starts on nought. The scoreboard shows at the end of a game
  and in the lobby between games.
- **Your own packs.** *Custom Packs…* in the lobby opens a window where the host pastes
  one question per line as `question | a | b | c | d | correct` (A–D or 1–4). Lines that
  cannot be read are named by number rather than dropped. Packs live in this browser, ride
  in a backup, and are copied onto the room when a game starts from one — so players, and a
  host who takes over mid-game, have the questions.
- **A hidden tab is right the moment you look at it.** The countdown is worked out from the
  server's timestamp and this browser's measured clock offset, never from a counter that a
  backgrounded tab would have slowed.
- **The demo room plays too:** its invented players answer at their own pace — one always
  right and quick, one right half the time and slow — so a whole game can be watched with
  nobody else there.

**Milestone 1 — the lobby (27 October 2026).** A room you can actually be in:

- **Start or join.** *Start a Room* opens a room with a six-letter code and makes you its
  host; *Join* takes a code. Your nickname is remembered, and a reload drops you straight
  back into the room you were in. A nickname somebody else is already wearing gets a digit.
- **Who's here.** A roster in arrival order with an online dot per player — green while
  that browser has written in the last four minutes, grey after — the host badged, you
  marked. Every player's browser writes a heartbeat every 90 seconds; any other write
  counts as one.
- **Invite.** The ↗ Invite button in the header (and on the room card) shows the code big
  enough to read out over the call and a link — `…/break-room/#join=CODE` — with Copy and,
  where the browser has it, Share. Opening the link drops you straight into the room if
  you have a nickname saved, or onto the home card with the code filled in if not.
- **Six hours, extendable.** The room card counts down to the expiry in minutes; the host
  can add two hours at a time up to twelve, and the button withdraws itself at the limit.
- **Leave.** As a player, your records go. As the host with others online, the room is
  handed to whoever has been there longest first; as the last one out, the room is closed.
- **Host handoff.** If the host's browser goes quiet for three minutes, the longest-present
  online player takes the seat automatically, and a player whose host has stopped moving a
  game on is told so after ten seconds rather than left with a frozen countdown.
- **A demo room.** *Try a Demo Room* on the home card opens a room of invented players —
  one of them away — so the lobby can be seen with no cloud, no console and no second
  browser. Nothing in it is saved.

**Milestone 0 — the scaffold (27 October 2026).** The family's chrome and guards: the
four-theme palette with Auto as the default, the one-line header, Back Up & Restore (your
nickname and custom packs), *Forget This Browser*, the help dot, the toast, the offline
worker, the install manifest, room codes from an alphabet with no O/0/I/1, the boundary
allowlists for everything that arrives from another browser, `firestore.rules` argued
clause by clause, and a suite with a floor (227 tests as of milestone 6).

All six milestones of the plan are built.

## How a Room Works

- **One room, one host.** Whoever starts the room is its host. The host's browser runs each
  game and is the only thing that writes the room document; every player writes only their
  own presence record and their own answer. If the host goes quiet for three minutes, the
  player who has been there longest takes over — a reload of the host's page picks up where
  the room is, because nothing lives only in the host's memory.
- **Six hours, extendable to twelve.** A room expires six hours after it is started. The host
  can extend it two hours at a time from the lobby, up to twelve in all; the server refuses
  anything longer. An expired room cannot be joined, played or read — the rules refuse it.
  Leave deletes your own records, the last one out deletes the room, and the last drawing is
  wiped when a drawing game ends. A room everyone just closed the tab on is NOT deleted: the
  project is on the free Spark plan, which has no time-to-live clean-up (see step 5 below).
- **Online means "wrote in the last four minutes".** Every player's browser rewrites its
  presence record every 90 seconds, and that is what draws the dot. There is no video or
  voice: the call you are already on is the voice.
- **Nothing identifies you across nights — unless you sign in for a house.** Sign-in is
  anonymous — Firebase hands this browser a random id so it can tell your answers from the
  next person's — and a nickname is whatever you typed for the evening. A Google sign-in is
  optional and only adds houses; rooms work the same with or without one.

## Setting Up the Cloud

The module at the foot of `index.html` carries `FIREBASE_CONFIG` — Firebase's public web
client config for the project **`breakroom-3722e`** (created 27 October 2026), not a
secret: access is enforced by `firestore.rules`. Empty the braces and the app is fully
local with nothing ever sent anywhere. These are the console steps that made the project,
kept for the day it is rebuilt or a sibling app needs the same. **Done** marks what is in
place today.

1. **Create a Firebase project** of its own (one project per app is the family's rule, so a
   rules mistake in one app can never reach another), with Gemini and Google Analytics
   turned off. Add a *web app* to it and copy the `firebaseConfig` object into
   `FIREBASE_CONFIG`. *Done — `BreakRoom`, web app "Break Room (web)".*
2. **Authentication → Sign-in method:** enable **Anonymous** and nothing else, with *auto
   clean-up* on (anonymous accounts older than 30 days are deleted, which is also what keeps
   them off the usage quota). This is the opposite of League Night's instruction, on purpose
   — `CLAUDE.md` says why. *Done.*
3. **Authentication → Settings → Authorized domains:** add `eagleadams86.github.io`. *Done.*
4. **Firestore Database:** create it (Standard edition, `nam5` United States, production
   mode), then **Rules → paste `firestore.rules` from this repo → Publish.** Do this before
   the first room is opened. The file is ASCII-only on purpose: a paste once mangled its
   UTF-8 punctuation, and the file must match the console byte for byte. *Done.*
5. **Firestore → Time-to-live (in the Google Cloud console):** a TTL policy on the field
   `expiresAt` for each of the collection groups `rooms`, `players`, `answers` and `live`.
   **Not done — TTL policies need the pay-as-you-go Blaze plan, and Charles decided on
   27 October 2026 that the project stays on the free Spark plan.** So a finished room is
   NOT deleted by Firestore: the rules refuse
   an expired room, the host closes the room when the last person leaves, and everyone's
   own records go when they leave, so what is left behind is only rooms people abandoned
   without pressing Leave — small documents, well inside the free 1 GiB. If the project
   ever moves to Blaze, set the four policies that day.
6. **Budget alert** (Google Cloud → Billing): the control for a heavy drawing night.
   **Not done — also needs a billing account, and the project stays on Spark.** There is
   nothing to bill; the free tier simply stops answering when a day's 50,000 reads are used
   up, which is the one thing a long drawing night (milestone 3) can run into.
7. **API key referrer restriction** (Google Cloud → APIs & Services → Credentials → the
   browser key → Websites): `https://eagleadams86.github.io/*` and `http://localhost:8026/*`.
   A speed bump against casual reuse of the public config, not a boundary — the rules are
   the boundary. *Done.*
8. Commit `FIREBASE_CONFIG`. The family's existing API-key alerts on GitHub are closed as
   won't-fix for exactly this reason; see memory before "fixing" one. *Done.*
9. **Google sign-in for houses (milestone 6):** Authentication → Sign-in method → **add
   Google** (support email = the project owner's). That creates an OAuth client; in Google
   Cloud → APIs & Services → Credentials → *Web client (auto created by Google Service)*, add
   **Authorized JavaScript origins** `https://eagleadams86.github.io` and
   `http://localhost:8026` — exact, port included, or Google refuses with `origin_mismatch`.
   Copy that client's id into `GOOGLE_CLIENT_ID` in the module (while it is `null` the Sign
   In button says sign-in isn't set up and nothing else changes). Then **re-publish
   `firestore.rules`**: it gained the `users` and `houses` blocks and a room's optional
   `house` label. A plain room never carries `house`, so the old rules keep working until
   the paste. Anonymous stays ON — rooms still need it. *Done 27 October 2026* — provider
   on (public-facing name "Break Room"), both origins added, client
   `168957055210-jnc950lb96j708unp11882eguus6iqi9`, rules published byte for byte.

The kill switch, if a room is ever abused: **Authentication → Sign-in method → Anonymous →
disable.** Every door closes at once, nothing is lost, and the rules can be tightened in the
console without a deploy.

Checking the console side from a terminal, without opening it (nothing here is a secret):

```bash
curl -s "https://identitytoolkit.googleapis.com/v1/projects?key=AIzaSyA_3hjhtgpdyLMN6M4sdUiqmkcBcLBRxDw"
```

Healthy: a JSON body whose `authorizedDomains` includes `eagleadams86.github.io`.

**First real run, 27 October 2026:** a room opened from one browser and joined from another
by its invite link, both anonymous; a ten-question game with answers from both sides scored
by the server's clock (133 and 111 after one question each); the host's page reloaded
mid-game and rejoined as host with the game still moving; and when the host's tab was closed
for three minutes, the other player's browser took the seat and ran the rest of the game.
The hidden-tab countdown is pinned by a test but has not yet been watched on a real
backgrounded tab.

**Drawing against the real cloud, 27 October 2026:** three separate browser profiles (three
anonymous players) on `localhost:8026`. The drawer's first stroke reached the host's screen
**0.3 seconds** after the pen went down; the host, guessing, never had the word on screen; a
third player who joined mid-turn got the whole drawing in one read; their guess list showed
"Ann got it" and Ann's wrong guess, never the word; and nothing was refused by the rules.

**Codenames against the real cloud, 27 October 2026:** four separate anonymous players. The
key was readable by the host and the two spymasters and **refused** (`permission-denied`)
to the operatives; the host left mid-game and the next player took the seat with the
spymasters still reading the key; every card turned matched the key the first host wrote;
the game ended on the assassin, the whole board went up for everyone and the key record was
emptied; no console errors. The first run also found that a team left with only its
spymaster stalled on its three-minute clocks — the reason for the "people come and go"
rules above.

## Testing It With More Than One Person

**Two tabs in one browser profile are ONE player.** The anonymous id lives in the browser's
own storage, so a second tab is the same uid, the same presence record and the same answer.
To play against yourself, open each extra player in a different Chrome profile or an
incognito window. The suite (`tests.html`) never loads the cloud: it boots the page in a
frame, and the module returns early inside a frame.

## Privacy and Security

- Every page ships a Content Security Policy that starts at `'none'`. The only script from
  off this origin is the Firebase SDK from `www.gstatic.com`, loaded by the module's own
  `import()`, and — only when a reader presses Sign In — Google Identity Services from
  `accounts.google.com` (League Night's exact CSP line). The page never talks to
  `firebaseapp.com`. The suite pins that host list exactly.
- A house is readable only by Google-signed accounts holding its code (`list: false`, as
  for rooms); a non-member may write only their own entry into its members, which is
  joining; only the founder deletes it. Removing a member is not offered: anyone with the
  code can join again, so a house that must be closed to someone is a new house.
- Everything rendered is escaped and every snapshot is sanitised at the boundary: a
  nickname, a question or an answer on your screen was typed in somebody else's browser.
- Nicknames, questions and answers are the only free text, each capped in code. There is no
  chat log, no notes field, and nothing from any work system.
- `privacy.html` says what is stored where and for how long, and is updated in the same
  commit as any change to it.

## Files

| | |
|---|---|
| `index.html` | the whole app: markup, stylesheet, the classic script (everything the suite can test) and the cloud module |
| `theme.css` | a byte-for-byte copy of the family theme pack's — never edited here |
| `firestore.rules` | the checked-in copy of the security rules; paste into the console |
| `privacy.html` | the privacy policy |
| `tests.html` | the suite; runs only on localhost |
| `sw.js`, `sw-kill.js` | the offline worker and its escape hatch |
| `manifest.webmanifest`, `icon-*.png`, `favicon.ico`, `make_icons.py` | the install manifest and the mark |
| `NOTICE`, `LICENSE` | provenance and the MIT terms |

## Running It Locally

```bash
python3 -m http.server 8026
```

Then open `http://localhost:8026/` for the app and `http://localhost:8026/tests.html` for
the suite. 8026 is this repo's port in both `.claude/launch.json` and the home
`~/.claude/launch.json`.

## Provenance

An independent personal project by Charles Adams — see `NOTICE` for authorship and
provenance and `LICENSE` for the MIT terms. Built from the family starter
(`eagleadams86/starter`); the shared chrome follows Sprint Predictability by way of Golf
Handicap, and the palette is the family theme pack's.
