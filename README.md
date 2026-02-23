# cowork.timer

A minimal, self-contained coworking Pomodoro timer for developers. No server, no account, no dependencies — just a single HTML file you can drop in a git repo and share with your team.

---

## How it works

The timer is **wall-clock anchored**, meaning it derives its current state from the real time of day rather than a countdown you start. This makes it naturally synchronized: two people who open the same session code at any point in time will always see the same phase and the same time remaining.

### Global session

When you open the page, there's always a **global session** running — a standard 25-minute Pomodoro with a 5-minute break, anchored at midnight so it cycles cleanly all day. No setup needed.

### Custom sessions

You can create a custom session by clicking **+ new session** and picking:

- **Name** — whatever you want to call it
- **Start time** — the HH:MM anchor for the cycle (everyone using the same code must be in the same timezone, or agree on UTC)
- **Work duration** — minutes per work block
- **Break duration** — minutes per break

Once created, the session appears in the left sidebar. Click it to switch. Multiple sessions can run in parallel — you just pick which one you're looking at.

---

## Sharing a session

Every session generates a **session code** shown below the timer. It looks like:

```
cowork:my%20sprint:09:00:50:10
```

This is a plain string that encodes the session's parameters. Anyone who pastes it into the **import field** in the "new session" modal will get the exact same timer — no sync, no server needed.

### How to share via git

The simplest workflow:

1. Create a session, copy the code
2. Paste it into a shared file in your repo — e.g. `COWORK.md` or `.cowork`
3. Commit and push
4. Teammates pull and paste the code into their local copy of the app

```bash
# Example .cowork file
cowork:backend%20sprint:09:30:50:10
```

Since the timer is wall-clock based, everyone who loads the code sees the same phase and remaining time instantly.

---

## Usage

### Running locally

Just open `cowork.html` in any browser. No build step, no dependencies, no internet required (except for loading the Google Fonts, which degrade gracefully).

```bash
open cowork.html
# or
python3 -m http.server  # then visit http://localhost:8000/cowork.html
```

### Progressive Web App support

The app is now installable as a standalone web application. When you open it over `http(s)` in a modern browser you can choose **"Install"** from the browser menu or click the **install** button that appears in the header. This registers a service worker and a web manifest (`manifest.json`), allowing you to launch the timer separately from your normal browser tabs like a native app. Make sure to host the files (for example via `python3 -m http.server` or GitHub Pages) so that the service worker can be registered.


### Hosting (optional)

Since it's a single file, you can host it anywhere static — GitHub Pages, Netlify, an S3 bucket, or even as a browser bookmark.

---

## Session code format

The session code is human-readable and reproducible:

```
cowork:{name}:{HH}:{MM}:{workMin}:{breakMin}
```

- `{name}` — URL-encoded session name
- `{HH}:{MM}` — 24h start time (the cycle anchor)
- `{workMin}` — work block length in minutes
- `{breakMin}` — break length in minutes

**Example:** A 50-minute work / 10-minute break session starting at 9:30 called "deep work":

```
cowork:deep%20work:09:30:50:10
```

---

## Timer behavior

- The timer is **always running** — it's derived from wall clock time, so there's no "start from zero" state to coordinate
- The **pause** button pauses the local visual tick only; it doesn't affect synchronization
- **Reset** just stops the local visual update; the shared state is unaffected
- **Skip** is local-only and doesn't affect other participants (by design — the shared state is the code, not a server)
- Cycle count increments automatically based on time elapsed since the start anchor

---

## Timezone note

Session codes don't include timezone information. Teams should agree on a reference timezone (UTC recommended) or simply use the same local time (which works if everyone is co-located or remote in the same zone). For cross-timezone teams, include the timezone in the session name as a convention, e.g. `deep work (UTC+0)`.

---

## Philosophy

This tool deliberately has no backend. Shared state lives in a string. That string lives in your repo. The timer is math, not memory. If your internet goes down, the timer keeps working. If your teammates are in different countries, they paste the same code and see the same phase. Simple things should be simple.

---

## Customization

The whole app is a single HTML file. Edit freely:

- **Colors** — CSS variables at the top of the `<style>` block
- **Default Pomodoro duration** — change `workMin: 25` and `breakMin: 5` in `globalSession()`
- **Fonts** — swap out the Google Fonts `<link>` and update `font-family` in `:root`
- **Cycle dot count** — change the `8` in `Math.min(cycleNum + 1, 8)` in `renderTimer()`

---

## License

Do whatever you want with it.
