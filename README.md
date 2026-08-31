# Send-to-Xeneon YouTube remote

Two tiny pages, no server to run:

- **player.html** — the page you point iCUE's iFrame widget at, *once*. It quietly polls a GitHub Gist and swaps the embedded video whenever the gist changes.
- **remote.html** — a mobile-friendly page. Paste a YouTube link, tap Send, done. Bookmark it (or "Add to Home Screen") on your phone or the PC browser.

They talk to each other through one GitHub Gist acting as a tiny shared "now playing" file. No account beyond GitHub, no hosting bill.

## Setup (10 minutes, one time)

**1. Create the gist**
Go to gist.github.com → New gist → filename `current.json` → content:
```json
{"videoId": "dQw4w9WgXcQ"}
```
Save as secret gist. Copy the **gist ID** from the URL (`gist.github.com/you/`**`THIS_PART`**).

**2. Create a token**
GitHub → Settings → Developer settings → Fine-grained tokens → New token.
- Repository access: none needed
- Permissions → Account permissions → **Gists: Read and write**
Copy the token (starts with `github_pat_…`).

**3. Host the two HTML files somewhere static**
Easiest: push this folder to a public GitHub repo and turn on **GitHub Pages** (Settings → Pages → deploy from branch). You'll get URLs like:
```
https://yourname.github.io/xeneon-yt/player.html
https://yourname.github.io/xeneon-yt/remote.html
```
(Cloudflare Pages / Netlify drag-and-drop work just as well if you'd rather not use Pages.)

**4. Point iCUE at the player, once**
In iCUE, add the iFrame widget on the Xeneon Edge and set its URL to:
```
https://yourname.github.io/xeneon-yt/player.html?gist=YOUR_GIST_ID&token=YOUR_TOKEN&poll=15
```
You never touch iCUE again after this.

**5. Set up the remote on your phone**
Open `remote.html`, tap Settings, enter the same Gist ID + token, tap Save. Add the page to your home screen for one-tap access.

## Using it
Copy any YouTube link → open the remote page → paste → Send. The screen picks it up within `poll` seconds (default 15).

## Notes
- The token lives in the widget URL (stored in iCUE) and in your phone's localStorage — not shared anywhere else. Use a fine-grained token scoped to gists only, so a leak can't do much.
- If you'd rather not put a token in the iCUE widget URL at all, you can make the gist **public** and drop `&token=` from the player URL — reads don't need auth then, only the remote's writes do.
- 15s polling with an authenticated request stays comfortably under GitHub's 5,000/hr rate limit.
