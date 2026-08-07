# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static personal portfolio site (no build tools, no package manager, no framework). Plain HTML/CSS, deployed as-is.

## Commands

There is no build, lint, or test tooling. To preview locally, just open the HTML files directly in a browser, or serve the directory with any static file server (e.g. `python -m http.server`) from the repo root.

## Structure

- `index.html`, `work.html`, `about.html`, `contact.html` — top-level pages.
- `case-*.html` — individual case study pages (one per project: CrediShield, Healthcare Co-Pilot, Weather ETL dashboard). New case studies follow this same `case-<slug>.html` naming and structure.
- `assets/css/style.css` — single shared stylesheet for the entire site (no per-page CSS, no preprocessor).
- `assets/img/` — screenshots and demo GIFs referenced by case study pages.

Every page repeats the same nav/header markup inline (no templating layer) — `<nav class="nav">` with links to Home/Work/About/Contact. When editing shared UI (nav, footer, fonts), update it in each HTML file individually.

Fonts are loaded from Google Fonts CDN (`Space Grotesk` for headings, `Inter` for body) via `<link>` tags in each page's `<head>`.
