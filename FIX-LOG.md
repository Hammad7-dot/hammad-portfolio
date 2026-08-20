# Mobile-Friendliness Fix Log — hammad-portfolio

Audited: https://hammad7-dot.github.io/hammad-portfolio/
Date: 2026-08-20

## Method
- Cloned the repo, read every HTML/CSS file, and rendered each page at a real
  phone viewport (390×844, 3x pixel density — iPhone 13 profile) using a
  headless browser, comparing before/after screenshots.
- Computed WCAG contrast ratios programmatically for every text/background
  color pair in the stylesheet.
- Checked every internal link, external demo link, and repo link.
- Measured every image/gif asset size and re-encoded the oversized ones.

## What was already good (no changes needed)
- Viewport meta tag (`width=device-width, initial-scale=1.0`) present on
  every page.
- Mobile nav already collapses into a "Menu" toggle under 640px.
- `img, video { max-width: 100% }` global rule — no images were overflowing
  or causing horizontal scroll on any width tested (375px–1440px).
- Base font size 16px with 1.6 line-height — comfortable, no changes needed.
- Body text contrast (#12141A on #FAFAFA) — 17.6:1, far above the 4.5:1 AA
  minimum.
- All internal nav links, case-study cross-links, resume link, LinkedIn,
  and GitHub-profile links resolve correctly.

## Problems found and fixed

### 1. Four demo GIFs were 1.6–4.5MB each (12.6MB total)
This was the biggest real mobile problem: on a phone connection, the
homepage alone pulled a 3.5MB GIF before it finished rendering, and the
Work page loaded all four full GIFs at once (12.6MB) just to show them as
small thumbnails.

**Fix:** converted all four screen recordings to compressed H.264 MP4
(via ffmpeg, `crf 28`) and extracted a static poster-frame JPG for each.
- Case-study pages and the homepage now use
  `<video autoplay loop muted playsinline poster="...">` — same visual
  effect, 91–94% smaller.
- The Work page grid, which only ever needed a static thumbnail, now loads
  the tiny poster JPG instead of the full animation, plus `loading="lazy"`
  so off-screen cards don't fetch until scrolled into view.

| File | Before | After (video) | Reduction |
|---|---|---|---|
| credishield-demo | 3.55 MB | 0.23 MB | 93% |
| crypto-dashboard-demo | 2.87 MB | 0.25 MB | 91% |
| healthcare-copilot-demo | 1.64 MB | 0.13 MB | 92% |
| weather-dashboard-demo | 4.55 MB | 0.29 MB | 94% |
| **Total (assets/img folder)** | **~13.6 MB** | **~2.3 MB** | **~83%** |

### 2. Failed color contrast on section labels
The green "eyebrow" labels (`PROOF POINT`, `WORK`, `CASE 01 · ...`, etc.)
used `#2FBF71` directly on the `#FAFAFA` background — a contrast ratio of
**2.28:1**, well under the WCAG AA minimum of 4.5:1 for small text. On a
phone in bright light this text was genuinely hard to read.

**Fix:** added a `--accent-text: #1a7d49` variable (a darker shade of the
same green) and applied it to `.eyebrow` and `.card-tag`. New ratio:
**4.94:1** — passes AA, still reads as the same brand green. Decorative
elements (the accent dot, button hover) kept the original brighter green
since those aren't text.

### 3. Mobile menu button was under the 44×44px tap-target minimum
`.nav-toggle` had only `padding: 0.5rem` around 14px text — roughly
30×56px, and short vertically. Small but real: on a real phone this is
the kind of button that takes two taps to hit.

**Fix:** added `min-height: 44px; min-width: 44px` and slightly more
padding, matching Apple/Google's minimum touch-target guidance.

### 4. Streamlit demo links — not a code bug, flagging anyway
The three "See it live →" links (CrediShield, Healthcare Co-Pilot, Weather
Dashboard) point to Streamlit Community Cloud apps. Free-tier Streamlit
apps go to sleep after a period of inactivity and show a "Zzzz, this app
has gone to sleep" wake-up screen that takes 20–60 seconds to load on
click — this isn't something fixable in the portfolio's code, it's the
hosting tier. Since every case study already embeds a real screen
recording, a visitor never depends on the live link to see the demo
working, so this is low risk — but worth knowing before you send this to
a recruiter who might click it cold. Options if you want to close this
gap: ping the three apps every few days to keep them warm, or note "may
take a few seconds to wake up" next to the link.

### Not touched (flagged only)
`assets/img/` also contains 10 screenshots (`taskapi-*.png`, `mcp-*.png`,
`weather-dashboard-live.png`, ~1.1MB total) that aren't referenced by any
page — dead weight in the repo, but since they're never fetched by a
visitor they don't affect load time or mobile experience, so I left them
in place rather than guessing whether you want them for something later.

## Result
- Total `assets/img` payload: **~13.6MB → ~2.3MB** (83% smaller)
- Homepage's first meaningful asset (hero GIF → video): **3.55MB → 0.23MB**
- One contrast failure fixed (2.28:1 → 4.94:1)
- One tap-target fixed (~30×56px → 44×44px minimum)
- Zero broken internal/nav/resume/social links found
