# Local SpectrumViewer Setup

## Overview

Instructions for running the GRIFFINCollaboration SpectrumViewer locally, connected to a local `dragon-replay` backend.

## Prerequisites

- Python 3 (for local HTTP server)

## Initial Clone

Both repositories must sit in the same parent directory:

```bash
mkdir -p ~/Work/GRIF-REPLAY
cd ~/Work/GRIF-REPLAY

git clone https://github.com/GRIFFINCollaboration/grif-replay.git
git clone https://github.com/GRIFFINCollaboration/SpectrumViewer.git
```

Result:
```
~/Work/GRIF-REPLAY/
├── grif-replay/
└── SpectrumViewer/
```

Then build `dragon-replay` inside `grif-replay`:

```bash
cd grif-replay
make
```

## Copying Required Data Files

Two files are not tracked by git and must be copied manually into `grif-replay/`:

**`last.json`** — analyzer configuration (histograms, directories, variables). Copy from another machine or a previous setup:

```bash
scp user@remote:/path/to/grif-replay/last.json ~/Work/GRIF-REPLAY/grif-replay/
```

Then update the three directory paths inside it to match your local machine (see One-time Code Changes below).

**`run9991.mid`** (or whichever MIDAS run file you want to replay) — copy from the data storage host, e.g. from smaug:

```bash
# Run this on smaug to push the file to your machine
scp /path/to/run9991.mid ruiz@<your-local-ip>:~/Work/GRIF-REPLAY/grif-replay/
```

Your local IP on the lab network can be found with:

```bash
ipconfig getifaddr en0
```

## One-time Code Changes

### 1. `grif-replay/last.json` — directory paths

Change all three directory paths from the default (`/Users/dragon/packages/grif-replay`) to the local machine's path:

```json
{"Directories" : [
   {"name" : "Data",   "Path" : "/Users/ruiz/Work/GRIF-REPLAY/grif-replay"},
   {"name" : "Histo",  "Path" : "/Users/ruiz/Work/GRIF-REPLAY/grif-replay"},
   {"name" : "Config", "Path" : "/Users/ruiz/Work/GRIF-REPLAY/grif-replay"}
]}
```

### 2. `templates/analyzer-viewer/analyzer-viewer.js` — local page links

Lines 80–81: change the `spectrumViewer2` and `2dSpectrumTool` URLs from `https://griffincollaboration.github.io/SpectrumViewer/` to the local server:

```js
var URLStringViewer    = 'http://localhost:8080/spectrumViewer2.html?...';
var URLString2DViewer  = 'http://localhost:8080/2dSpectrumTool.html?...';
```

## Starting the Local Server

The SpectrumViewer must be served over HTTP (not opened as a `file://` URL) or the browser will block requests to the backend.

```bash
cd /Users/ruiz/Work/GRIF-REPLAY/SpectrumViewer
python3 -m http.server 8080
```

## Connecting

Open in browser:

```
http://localhost:8080/analyzerInterface.html?backend=localhost&port=9093
```

The `backend=localhost` parameter causes the JS to construct `http://localhost:9093` as the server URL. Do **not** combine them as `backend=localhost:9093` — that breaks the URL parser in `helpers.js`.
