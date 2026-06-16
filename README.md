# CI-CD-Design-Recherche
# ci-kit-workflow

A battle-tested agency workflow for researching client brand identities, building HTML overview pages, generating style guides, and deploying to a web server via SFTP.

Built for AI-assisted design workflows (Claude, Hermes, or similar agents), but the patterns are solid for any structured design-to-deploy pipeline.

---

## What's in here

**`SKILL.md`** — The main workflow document. Covers:

- **Mode 1: CI Research** — How to structure brand/CI research into a `ci-kit.md`
- **Mode 3: Overview Page** — HTML template patterns for client overview pages
- **Mode 4: Style Guide** — Generating a separate `styleguide.html` per project
- **CSS Architecture** — Token system, dark mode, CSS/JS sync, extraction from inline styles
- **Build Process** — Template adaptation, patch workflow, post-build audit checklist
- **SFTP Deployment** — Python/paramiko deploy patterns, safety rules, remote structure checks
- **Local Dev** — Local HTTP server workflow, link auditing before deploy
- **Playwright Rendering** — HTML → PNG screenshots for social media assets
- **Pitfalls** — 20+ documented failure modes with symptoms, causes, and fixes

---

## Who this is for

Small agencies or freelancers who:
- Build HTML overview pages for multiple clients
- Maintain a consistent design system across projects
- Deploy via SFTP to a shared or VPS host
- Work with AI agents on design and dev tasks

---

## Project structure (per client)

```
workspace/clients/
└── clientname/
    ├── SOUL.md                ← Agent entry point
    ├── docs/
    │   ├── ci-kit.md          ← Brand research
    │   ├── DESIGN.md          ← Design system spec
    │   └── tokens.json
    └── web/                   ← Local dev = production structure
        ├── index.html
        ├── styleguide.html
        ├── css/
        │   ├── style.css      ← Design tokens (global)
        │   └── ...
        ├── js/main.js
        └── img/
```

---

## Key principles

- **One source of truth for tokens** — `:root` only in `style.css`, never in page-specific CSS
- **Style guide = separate page** — not embedded in the overview page
- **Local before live** — always test on `localhost:8765` before deploying
- **Patch after build** — build scripts never get everything right on the first pass; expect 5–15 follow-up patches
- **Relative paths everywhere** — absolute paths break local dev
- **paramiko, not shell** — SFTP passwords with special characters break shell escaping

---

## License

MIT
