# Break Room

A room your team drops into beside the Teams or Zoom call you already have open. Pick a
nickname, start a room or join one with its six-letter code, see who's around, and let the
host run a round of trivia while everyone talks. No account, nothing to install, and
everything in a room is deleted within a day.

**Live: https://eagleadams86.github.io/break-room/** · **Privacy:** [privacy.html](https://eagleadams86.github.io/break-room/privacy.html)

Built for two groups at once — a distributed work team on corporate laptops and a friends
group — so it never needs a Google account, never opens a video stream, and never asks for
anything from a work system. The plan is in
`~/.claude/plans/there-used-to-be-flickering-lovelace.md`; `CLAUDE.md` records what this app
decides that the rest of the family has not.

## What It Does Today

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
clause by clause, and a suite with a floor (131 tests as of milestone 2).

**Coming next:** drawing & guess (milestone 3), icebreakers (4), Codenames (5), and last
of all Google sign-in for a standing house with a cross-night leaderboard (6).

## How a Room Works

- **One room, one host.** Whoever starts the room is its host. The host's browser runs each
  game and is the only thing that writes the room document; every player writes only their
  own presence record and their own answer. If the host goes quiet for three minutes, the
  player who has been there longest takes over — a reload of the host's page picks up where
  the room is, because nothing lives only in the host's memory.
- **Six hours, extendable to twelve.** A room expires six hours after it is started. The host
  can extend it two hours at a time from the lobby, up to twelve in all; the server refuses
  anything longer. An expired room cannot be joined or played, and Firestore deletes it, with
  every record in it, within about a day.
- **Online means "wrote in the last four minutes".** Every player's browser rewrites its
  presence record every 90 seconds, and that is what draws the dot. There is no video or
  voice: the call you are already on is the voice.
- **Nothing identifies you across nights.** Sign-in is anonymous — Firebase hands this
  browser a random id so it can tell your answers from the next person's — and a nickname is
  whatever you typed for the evening.

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
   **Not done — TTL policies need the pay-as-you-go Blaze plan, and the project is on
   Spark.** Until that changes, a finished room is NOT deleted by Firestore: the rules refuse
   an expired room, the host closes the room when the last person leaves, and everyone's
   own records go when they leave, so what is left behind is only rooms people abandoned
   without pressing Leave — small documents, well inside the free 1 GiB. Set the four
   policies the day the project moves to Blaze.
6. **Budget alert** (Google Cloud → Billing): the control for a heavy drawing night.
   **Not done — also needs a billing account.** On Spark there is nothing to bill; the free
   tier simply stops answering when a day's 50,000 reads are used up.
7. **API key referrer restriction** (Google Cloud → APIs & Services → Credentials → the
   browser key → Websites): `https://eagleadams86.github.io/*` and `http://localhost:8026/*`.
   A speed bump against casual reuse of the public config, not a boundary — the rules are
   the boundary. *Done.*
8. Commit `FIREBASE_CONFIG`. The family's existing API-key alerts on GitHub are closed as
   won't-fix for exactly this reason; see memory before "fixing" one. *Done.*

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
mid-game and rejoined as host with the game still moving. The three-minute handoff and the
hidden-tab countdown were checked the same afternoon — see `CLAUDE.md`.

## Testing It With More Than One Person

**Two tabs in one browser profile are ONE player.** The anonymous id lives in the browser's
own storage, so a second tab is the same uid, the same presence record and the same answer.
To play against yourself, open each extra player in a different Chrome profile or an
incognito window. The suite (`tests.html`) never loads the cloud: it boots the page in a
frame, and the module returns early inside a frame.

## Privacy and Security

- Every page ships a Content Security Policy that starts at `'none'`. The only script from
  off this origin is the Firebase SDK from `www.gstatic.com`, loaded by the module's own
  `import()`; the page talks to three Google endpoints and nothing else, and never to
  `firebaseapp.com`. The suite pins that host list exactly.
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
