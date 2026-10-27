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

**Milestone 0 — the scaffold (27 October 2026).** The page runs, fully local, with the
family's chrome and guards in place:

- **The home card.** A nickname and two doors: *Start a Room* and *Join* with a six-letter
  code. Recent codes come back as buttons. Until the cloud is connected (see below) both
  doors are disabled and the card says why.
- **Room codes.** Six characters from an alphabet with no O, 0, I or 1 — the same one League
  Night reads out over a phone — drawn from `crypto.getRandomValues`. Knowing the code is the
  only way into a room: the rules never let anyone list rooms.
- **The data guards.** Everything that will arrive from another browser — the room, the
  player list, the answers — comes through an allowlist that rebuilds rather than merges,
  with every uid shape-checked, every string capped and every list bounded. A room written
  by a newer build halts the page rather than being quietly rewritten by an old host.
- **`firestore.rules`**, argued clause by clause: anonymous sign-in, the code as the read
  capability, the host as the only writer of the room by `rev + 1` transactions, players
  writing only their own documents with server-timestamp sentinels, and a handoff clause
  for a host who goes quiet.
- **The family's furniture:** the four-theme palette with Auto as the default, the one-line
  header, Back Up & Restore (your nickname and custom packs), *Forget This Browser*, the
  help dot, the toast, the offline worker, the install manifest, and a 94-test suite with a
  floor.

**Coming next:** the lobby with online dots, heartbeat and host handoff (milestone 1), then
trivia with four embedded packs and a custom-pack editor (milestone 2), then drawing &
guess, icebreakers and Codenames.

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

The module at the foot of `index.html` carries `FIREBASE_CONFIG` (Firebase's public web
client config, not a secret — access is enforced by `firestore.rules`). While it is `null`
the app is fully local and nothing is ever sent anywhere. These are the console steps that
turn it on; none of them can be done from a terminal, so they wait for Charles:

1. **Create a Firebase project** of its own (one project per app is the family's rule, so a
   rules mistake in one app can never reach another). Add a *web app* to it and copy the
   `firebaseConfig` object into `FIREBASE_CONFIG`.
2. **Authentication → Sign-in method:** enable **Anonymous** and nothing else. (This is the
   opposite of League Night's instruction, on purpose — `CLAUDE.md` says why.)
3. **Authentication → Settings → Authorized domains:** add `eagleadams86.github.io`.
4. **Firestore Database:** create it in production mode, then **Rules → paste
   `firestore.rules` from this repo → Publish.** Do this before the first room is opened.
5. **Firestore Database → Time-to-live:** add a TTL policy on the field `expiresAt` for each
   of the collection groups `rooms`, `players`, `answers` and `live`. This is what deletes a
   finished room; the rules refuse an expired one long before the deletion runs.
6. **Google Cloud Console → Billing → Budgets & alerts:** a budget alert on the project (the
   drawing game, when it arrives, can exceed the free tier's 50,000 reads a day on a long
   night — the alert is the control).
7. **Google Cloud Console → APIs & Services → Credentials → the browser API key:** under
   *Application restrictions* choose HTTP referrers and add `https://eagleadams86.github.io/*`
   and `http://localhost:8026/*`. A speed bump against casual reuse of the public config, not
   a boundary — the rules are the boundary.
8. Commit `FIREBASE_CONFIG`. The family's existing API-key alerts on GitHub are closed as
   won't-fix for exactly this reason; see memory before "fixing" one.

The kill switch, if a room is ever abused: **Authentication → Sign-in method → Anonymous →
disable.** Every door closes at once, nothing is lost, and the rules can be tightened in the
console without a deploy.

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
