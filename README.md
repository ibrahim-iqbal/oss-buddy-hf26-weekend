<div align="center">

# OSS Buddy

### Point it at a repo. Get three weekend-sized issues. Nothing uploaded.

[![License: MIT](https://img.shields.io/badge/License-MIT-c25a3f.svg)](./LICENSE)
[![Hacktoberfest 2026 — Weekend](https://img.shields.io/badge/Hacktoberfest-Weekend%20Build%20for%20a%20Friend-c25a3f.svg)](https://dev.to/challenges/hacktoberfest-weekend-2026-10-01)
[![Powered by Gemma 3 4B](https://img.shields.io/badge/powered%20by-Gemma%203%204B-c25a3f.svg)](https://ai.google.dev/gemma)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-c25a3f.svg)](#contributing)

[**DEV write-up**](https://dev.to/ibrahimiqbal/oss-buddy-a-local-gemma-that-picks-weekend-sized-issues-so-my-cousin-can-finally-land-his-first-pr-277f)

</div>

<p align="center">
  <img src="./demo.gif" alt="OSS Buddy — one command, repo in, three weekend-sized issues ranked easiest to hardest" width="820">
</p>

<p align="center">
  <sub><em>a 150-line local Gemma CLI that reads any GitHub repo and picks weekend-sized good-first-issues.</em></sub>
</p>

---

I have a cousin. He has been telling me he wants to contribute to open source for about a year. Every month or two he sends me a screenshot of a repo and asks "is this a good one to start with?" I say yes. He opens it, scrolls for twenty minutes, closes the tab. The screenshot never turns into a PR.

I used to tell him "just pick a good first issue." That advice is useless. Good first issues live in repos with four hundred files and eighty open issues each. You still have to pick. He had already been doing that for a year.

So I built **OSS Buddy**. You point it at any GitHub repo and a local Gemma 3 model tells you three things in about twenty-three seconds:

1. What the project actually does, in two plain sentences
2. Up to three `good first issue` tickets ranked easiest to hardest, with time estimates
3. Which specific directory to start reading

Nothing leaves his laptop. No account, no API key, no credit card. He downloads Gemma once, forgets about it, the tool works forever.

## Features

- **One command. Three HTTP calls to GitHub, one to Ollama on localhost.** The whole pipeline is small enough to read in one sitting.
- **~23 seconds end-to-end on an M-series Mac**, benchmarked on 5 real repos (kiwix/kiwix-android, OpenNutriTracker, RetroMusicPlayer, neovim, fastapi).
- **Three label-spelling fallbacks** (`good first issue` / `good-first-issue` / `beginner`). Doubled the repos the tool works on.
- **Honest zeros.** Repos with no `good-first-issue` label (like neovim) or with none open get a plain "none" instead of hallucinated issues. The prompt pins Gemma: *"Quote the issue number and the EXACT title. Do not paraphrase or invent."*
- **~150 lines of stdlib Python.** No `pip install`.

## Benchmarks

Measured end-to-end, one command, no warmup, no cache. Gemma 3 4B via Ollama on an M-series Mac.

| Repo | Stack | Runtime | Good-first-issues found |
|------|-------|--------:|------------------------:|
| kiwix/kiwix-android | Kotlin | 27.9s | 6 |
| simonoppowa/OpenNutriTracker | Flutter | 23.5s | 3 |
| RetroMusicPlayer/RetroMusicPlayer | Kotlin | 22.1s | 6 |
| neovim/neovim | C | 22.0s | 0 (no `good first issue` label) |
| fastapi/fastapi | Python | 22.0s | 0 (none open at test time) |

Average: **~23 seconds**, zero cloud bytes.

## Try it

**Requirements:** Python 3.9+, [GitHub CLI (`gh`)](https://cli.github.com), [Ollama](https://ollama.com).

```bash
git clone https://github.com/ibrahim-iqbal/oss-buddy-hf26-weekend.git
cd oss-buddy-hf26-weekend
ollama pull gemma3:4b
python3 oss_buddy.py <owner>/<repo>
```

Example:

```bash
python3 oss_buddy.py kiwix/kiwix-android
```

## How it works

```
    gh api  (readme + top-level tree + good-first-issues)
                        │
                        ▼
                 one tuned prompt
                        │
                        ▼
         POST localhost:11434 (ollama)
                        │
                        ▼
                   gemma 3 4b
                        │
                        ▼
         stream the answer to the terminal
```

Three HTTP calls to GitHub, one to a server on your own laptop. That's the whole pipeline.

## Stack

- **Open-weight model:** [Gemma 3 4B](https://ai.google.dev/gemma) by Google
- **Runtime:** [Ollama](https://ollama.com) — open-source
- **Repo data:** `gh` CLI, three API calls (readme, tree, issues)
- **Code:** stdlib Python, no `pip install`

## Honest limits

- Needs Ollama + the Gemma 3 4B model (~2.5 GB) locally.
- Only reads top-level file tree and open `good-first-issue`-labelled tickets. Doesn't yet trace linked files or recent PR diffs — on the roadmap.
- "The friend" (my cousin, the person this was built for) hasn't actually used it as of this writing. The DEV post states that plainly rather than dressing it up.

## Acknowledgements

- **[Gemma 3](https://ai.google.dev/gemma)** by Google — the only model used.
- **[Ollama](https://ollama.com)** — the open runtime that makes local inference boring in a good way.
- **[GitHub CLI](https://cli.github.com)** — for the three API calls.
- **The Hacktoberfest Weekend Challenge: Build for a Friend** — the forcing function.

## License

[MIT](./LICENSE).

---

<p align="center">
  <sub><em>crafted by <a href="https://github.com/ibrahim-iqbal">ibrahim-iqbal</a> · <a href="https://dev.to/ibrahimiqbal">dev.to</a></em></sub>
</p>
