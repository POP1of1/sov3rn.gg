# site/ — GitHub Pages for sov3rn.gg

Published from `main`, folder `/site`, custom domain **sov3rn.gg** (the `CNAME` file
here is what GitHub reads). Static only: no build on GitHub's side, no Jekyll
(`.nojekyll`), no analytics, no cookies.

| Path | Serves | Source |
|---|---|---|
| `index.html` | `https://sov3rn.gg/` → instant redirect to `/arena/` | hand-written, edit in place |
| `arena/index.html` | `https://sov3rn.gg/arena/` — the Start Here deck | **generated** from `framework/START_HERE_arena.html` |
| `arena/rulebook.html` | `https://sov3rn.gg/arena/rulebook.html` | **generated** from `framework/ARENA_RULEBOOK.html` |
| `arena/og-arena.png` | 1200×630 social card | static (Arena room's site_handoff v3) |
| `arena/favicon.png` | 64×64 tab icon | static (same) |
| `CNAME` | `sov3rn.gg` | static |

## Updating the deck or the rulebook — one command

The framework files are the canon. Edit them, then regenerate:

```
make site            # or: python tools/build_site.py
git add site && git commit -m "site: regenerate arena pages" && git push
```

Pages redeploys on the push (usually under a minute). `tools/build_site.py` lifts each
fragment's `<title>` into a full `<head>` (viewport, description, favicon, canonical,
Open Graph / Twitter cards pointed at `https://sov3rn.gg/arena/…`) and wraps the rest
in `<body>` — the exact shape of the Arena room's handoff with the placeholders
resolved. It never edits the fragment itself. Never hand-edit `arena/*.html`; the next
build overwrites them.

Static files (`og-arena.png`, `favicon.png`, `CNAME`, the root redirect) are not
regenerated — edit them in place.

## Hosting — the plan gate (found 2026-09-09)

`sovrn-bot` is a **private** repo on the GitHub **Free** plan, and Pages on a private
repo needs Pro/Team — the API refuses with *"Your current plan does not support GitHub
Pages for this repository."* Two ways through, operator's call:

1. **Upgrade the account to GitHub Pro** (~$4/mo) → Settings → Pages → Source: `main`,
   folder `/site` → custom domain `sov3rn.gg` → Enforce HTTPS. Nothing else changes.
2. **A public site repo** (free): create an empty public repo
   `github.com/POP1of1/sov3rn.gg`, then from this repo run

   ```
   make site-publish            # pushes ONLY the site/ subtree as that repo's main
   # no `make` on the box (Windows)? the same two commands:
   python tools/build_site.py
   git push -f https://github.com/POP1of1/sov3rn.gg.git $(git subtree split --prefix=site main):main
   ```

   and in that repo: Settings → Pages → Source: `main`, folder `/ (root)` → custom domain
   `sov3rn.gg` → Enforce HTTPS. Every later update is `make site` here, commit, then
   `make site-publish`. The bot, briefs, and data never leave the private repo — only
   `site/` is pushed.

## DNS (apex on GitHub Pages)

Apex `sov3rn.gg` → four `A` records and four `AAAA` records to GitHub's Pages edge;
`www` → `CNAME pop1of1.github.io`. The exact records are in the repo's Pages setup
report (2026-09-09) and in GitHub's docs under "Managing a custom domain". After the
records propagate, GitHub issues the TLS certificate automatically; then tick
**Enforce HTTPS** in Settings → Pages if it is not already on.

## Checking a deploy

```
nslookup sov3rn.gg                       # A records = 185.199.108–111.153
curl -sI https://sov3rn.gg/arena/ | head -5
curl -s https://sov3rn.gg/arena/ | grep -o '<title>[^<]*'
```
