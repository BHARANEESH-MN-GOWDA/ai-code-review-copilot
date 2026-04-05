# CodeSentinel v4 — Complete Run Guide
> Single file. Everything you need to get it running in the terminal.

---

## What Kind of Project Is This?

CodeSentinel v4 is a **pure frontend web app** — HTML + CSS + JavaScript only.
There is no Node.js backend, no Python server, no build step, no npm install.
You just need a browser + a local static file server + an Anthropic API key.

---

## Prerequisites

### 1. A Terminal
Any terminal works — macOS Terminal, Linux bash, Windows WSL, Git Bash, PowerShell.

### 2. One of These (to serve the files locally)

Pick **any one** of the following — you only need one:

#### Option A — Python (most common, usually pre-installed)
```bash
python3 --version    # check if installed
# If not installed:
# macOS:   brew install python3
# Ubuntu:  sudo apt install python3
# Windows: https://python.org/downloads
```

#### Option B — Node.js / npx (if you already have Node)
```bash
node --version       # check if installed
# If not installed: https://nodejs.org  (download LTS)
```

#### Option C — VS Code Live Server (GUI option)
Install the "Live Server" extension in VS Code, then right-click `index.html` → Open with Live Server.

---

### 3. An Anthropic API Key

The app calls `api.anthropic.com` directly from the browser.
You need a key from: **https://console.anthropic.com/account/keys**

> Without a key the app still works — it falls back to rich mock/demo data automatically.

---

### 4. CORS Proxy (Required for API calls from browser)

Anthropic's API does **not** allow direct browser calls (CORS policy).
You need a local proxy to forward requests. Use one of these:

#### Option A — cors-anywhere (Node.js, quickest)
```bash
npx cors-anywhere
# Runs on http://localhost:8080
```

#### Option B — local-cors-proxy (npm global)
```bash
npm install -g local-cors-proxy
lcp --proxyUrl https://api.anthropic.com --port 8010
# Runs on http://localhost:8010
```

> **Note:** If you skip the proxy, the app will automatically fall back to demo data — it still fully works for presentation purposes.

---

## Step-by-Step: Running the Project

### Step 1 — Unzip the Project
```bash
unzip CodeSentinel_v4_Project.zip
cd codesentinel-project
```

Your folder structure should look like:
```
codesentinel-project/
├── index.html
├── css/
│   └── styles.css
└── js/
    ├── api.js
    ├── charts.js
    ├── exports.js
    ├── filters.js
    ├── helpers.js
    ├── init.js
    ├── inline-suggestions.js
    ├── mock.js
    ├── pipeline.js
    ├── render-panels.js
    ├── requirements.js
    ├── sidebar.js
    └── state.js
```

---

### Step 2 — Add Your Anthropic API Key (optional but recommended)

Open `js/api.js` in any text editor and find this line (~line 40):

```javascript
const r = await fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
```

Change the headers to include your key:

```javascript
const r = await fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key': 'sk-ant-YOUR_KEY_HERE',
    'anthropic-version': '2023-06-01',
    'anthropic-dangerous-direct-browser-access': 'true'
  },
```

> The `anthropic-dangerous-direct-browser-access: true` header is required by Anthropic
> to explicitly allow direct browser-to-API calls (acknowledging it's for dev/demo use).

---

### Step 3 — Start a Local File Server

Open a terminal, navigate to the project folder, and run ONE of these:

#### Python 3 (recommended)
```bash
cd codesentinel-project
python3 -m http.server 3000
```

#### Python 2 (older systems)
```bash
cd codesentinel-project
python -m SimpleHTTPServer 3000
```

#### Node.js / npx (no install needed)
```bash
cd codesentinel-project
npx serve . -p 3000
```

#### Node.js http-server (global install)
```bash
npm install -g http-server
cd codesentinel-project
http-server -p 3000
```

You should see output like:
```
Serving HTTP on 0.0.0.0 port 3000 (http://0.0.0.0:3000/) ...
```

---

### Step 4 — Open in Browser

Open your browser and go to:
```
http://localhost:3000
```

You should see the CodeSentinel v4 dashboard with the 🛡️ logo and input bar.

---

## Running It — Quick Copy-Paste Commands

### Full flow (Python, no API key — demo mode):
```bash
unzip CodeSentinel_v4_Project.zip
cd codesentinel-project
python3 -m http.server 3000
# Open browser: http://localhost:3000
# Click any demo button (expressjs, flask, django, spring) to see results
```

### Full flow (Node.js, no API key — demo mode):
```bash
unzip CodeSentinel_v4_Project.zip
cd codesentinel-project
npx serve . -p 3000
# Open browser: http://localhost:3000
```

---

## Using the App

Once open in the browser:

| Action | How |
|--------|-----|
| **Quick demo** | Click any of the 4 preset buttons (expressjs / flask / django / spring-boot) |
| **Custom repo** | Paste a GitHub URL → click ⚡ Analyze Repository |
| **Toggle engines** | Click any engine chip (AST, CVE, Security…) to enable/disable |
| **Filter results** | Use the FILTER tab in the left sidebar |
| **View file tree** | FILES tab in left sidebar |
| **View architecture** | ARCH tab in left sidebar |
| **Export report** | Right sidebar — JSON / Markdown / SARIF / HTML / CSV buttons |

---

## Troubleshooting

### "Cannot open index.html directly" (double-click doesn't work)
The app uses `fetch()` which requires HTTP — it won't work via `file://` URLs.
Always use the local server (`python3 -m http.server 3000`).

### API call fails / returns error
The app automatically falls back to mock data — you'll still see full results.
Check the browser console (F12) for error details.

### Port 3000 already in use
```bash
python3 -m http.server 8080    # use a different port
# Then open: http://localhost:8080
```

### CORS error in console
This is expected when calling Anthropic's API directly from the browser.
The `anthropic-dangerous-direct-browser-access: true` header suppresses it.
If still blocked, the mock fallback activates automatically.

### Blank page / nothing loads
Make sure you're inside the `codesentinel-project/` folder (the one with `index.html`) before running the server.

---

## OS-Specific Notes

### macOS
Python 3 is pre-installed on macOS 12+. Just run:
```bash
python3 -m http.server 3000
```

### Ubuntu / Debian Linux
```bash
sudo apt update && sudo apt install python3
python3 -m http.server 3000
```

### Windows (PowerShell)
```powershell
# If Python is installed:
python -m http.server 3000

# If Node.js is installed:
npx serve . -p 3000
```

### Windows (WSL)
Same as Linux. Access the app at `http://localhost:3000` in your Windows browser.

---

## Summary — Minimum to Get Running

```
1. Unzip the project
2. cd codesentinel-project
3. python3 -m http.server 3000
4. Open http://localhost:3000 in browser
5. Click a demo button → see full AI code review results
```

That's it. No npm install. No build step. No backend. No dependencies.
