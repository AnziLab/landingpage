# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

Static site served at `dev.anzilab.com` (see `CNAME`). It is a personal collection of standalone, single-file web apps — the landing page `index.html` links out to each tool. Most UI text is Korean.

There is **no build system, no package manager, no test suite, and no server code**. Every page is a self-contained `.html` file that runs directly in the browser.

## Running locally

Open any `.html` file directly in a browser, or serve the directory with any static server, e.g.:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/
```

Deployment is via GitHub Pages to the custom domain in `CNAME` — pushing to the default branch publishes the site; no build step runs.

## File layout conventions

- `index.html` — landing page. Apps are grouped into `<section id="...">` blocks (currently `music` and `media`). Some apps on disk are intentionally **unlinked** because they're too personal to surface publicly — notably `tvtodo.html`, `stopwatch.html`, `wpm.html`, `reading1.html`, `memorygame`, `commons.html`. Don't re-add them to the landing page without asking. Adding a new public app means dropping an `<a class="card" href="newfile.html">` into the matching section grid and bumping the section's count in `.section-meta`.
- `*.html` at the repo root — each file is one independent app. Do **not** factor shared code into separate JS/CSS files; the established pattern is that every page inlines its own `<style>` and `<script>`.
- `memorygame` (no extension) is also an HTML file, served as-is.
- `sounds/{piano,bass4,drums}/*.opus` — sample library. Note: the guitar practice apps currently synthesize tones via the Web Audio API (`AudioContext`, oscillators) rather than loading these samples. If you wire samples in, reference them as relative paths from the HTML file.
- `CNAME` controls the GitHub Pages domain — do not edit unless moving the site.

## The `practice_*` family

Several files implement overlapping "guitar scale / metronome / chord-progression" practice tools, each a different prototype:

- `gtpractice.html` — the most feature-complete version (~90 KB): fretboard, metronome, chord progressions, multiple instruments (`guitar`, `bass4`, `bass5`, `violin`), volume controls per voice, Web Audio synthesis.
- `fretboard.html` — simpler fretboard-only scale viewer.
- `practice_gpt.html`, `practice_gemini.html`, `practice_canva.html` — alternative HTML/Tailwind implementations of the same concept.
- `practice_claude.html` — **misleading extension**: this file contains raw React/JSX source (`import React from 'react'`, `lucide-react`), not runnable HTML. Treat it as a code sketch, not a deployable page. Don't link it from `index.html` unless it is rebuilt as static HTML.

When changing scale/chord logic, `gtpractice.html` is the canonical implementation.

## External dependencies (all CDN)

Pages pull libraries from CDNs at load time — there is no lockfile. Common ones in use:

- `https://cdn.tailwindcss.com` — many pages use Tailwind via the runtime CDN (not a build). Utility classes work, but `@apply` in `<style>` blocks will not (see `slowdowner.html` for an example where `@apply` is written but won't compile at runtime).
- `chart.js`, `chartjs-plugin-annotation`, `katex` — used by `commons.html`.
- Google Fonts + Material Symbols — used by `tvtodo.html` and others.

## Persistence

- `tvtodo.html` reads/writes to a **Google Apps Script** endpoint stored as `CONFIG.GAS_URL` (search for `GAS_URL`). Changing the backing sheet/script means updating that URL string.
- `tempo.html` uses `localStorage` for user preferences.
- No other page has server-side state.

## Conventions when editing

- Keep each app self-contained — inline CSS and JS in the same `.html` file. Don't introduce a shared stylesheet or module system.
- Preserve the existing Korean UI strings unless explicitly asked to translate.
- When adding a new app, also add a card to `index.html` so it's reachable.

## Working style for Claude Code on this repo

- **Orchestration**: Opus plans and directs; actual coding/review work is delegated to Sonnet subagents (via the Agent tool). Opus should not write large code edits directly.
- **Deployment target**: This site is served from the `main` branch via GitHub Pages. After each change set, merge and push to `main` so updates go live at `dev.anzilab.com`.
