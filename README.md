# docs-opspilot

Product documentation for **NubeStack OpsPilot** — an AI operations workbench for
DevOps, SRE, network, platform and development teams.

This repository contains documentation only. The product itself lives in a
separate repository.

Published site: <https://nubestack-collab.github.io/docs-opspilot/>

## Build it locally

```bash
python3 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt

mkdocs serve           # live preview on http://127.0.0.1:8000
mkdocs build --strict   # what CI runs; must pass before you push
```

`--strict` fails the build on a broken internal link or a page missing from the
nav. Keep it strict — a silently dead link in product documentation is worse
than a red build.

## Layout

```
mkdocs.yml                    theme, markdown extensions, nav
requirements.txt              pinned mkdocs, mkdocs-material, pymdown-extensions
docs/
  index.md                    landing page
  overview/                   what it is, why, who for, how it works, architecture
  getting-started/            requirements, install, first run, quickstart
  connections/                connection types, hypervisor consoles, RDP/VNC, groups, credentials
  workspace/                  sessions, terminal, built-in tools, files, S3
  ai/                         providers, offline Ollama, self-hosted, MCP assistants, remote/mobile
  safety/                     execution boundary, risk tiers, approvals, profiles, security model
  operations/                 role workflows, administration, backup/upgrade, troubleshooting
  reference/                  connection fields, settings, providers, MCP tools, patterns, glossary
  about/                      plans, release notes, support, licensing
  assets/images/              product screenshots
  stylesheets/extra.css       risk-tier badges, screenshot and caption styling
.github/workflows/docs.yml    strict build on every push and PR; deploys main to Pages
CLAUDE.md                     sources of truth, source-file map, product invariants
CONTRIBUTING.md               writing style, page conventions, review checklist
```

## Before you write a page

Read [`CLAUDE.md`](CLAUDE.md) and [`CONTRIBUTING.md`](CONTRIBUTING.md) first.

`CLAUDE.md` holds the facts: which product source file is authoritative for
which claim, the ten product invariants a page must never contradict, a table of
known inaccuracies in the upstream user guide, and the honesty rules this site
is written under.

`CONTRIBUTING.md` holds the craft: voice, page shape, the terminology table, how
to reference images and risk-tier badges, and the checklist a change has to pass.

The short version: the product source code is the authority, the user guide is
the narrative starting point, limitations get documented rather than marketed
around, and nothing gets invented — no prices, paths, flags, shortcuts or URLs
that are not in a source.

## Publishing

Pushes to `main` build and deploy to GitHub Pages automatically; pull requests
stop at the strict build and publish nothing.

No manual setup is needed. The deploy job runs `actions/configure-pages` with
`enablement: true`, which switches Pages on for the repository and sets the
source to **GitHub Actions** on the first run. If the very first run fails on
that step, the repository's Actions permissions are read-only — set **Settings →
Actions → General → Workflow permissions** to allow write access, or turn Pages
on by hand under **Settings → Pages** with **GitHub Actions** as the source, then
re-run the workflow.

The published URL is <https://nubestack-collab.github.io/docs-opspilot/>, which
is also `site_url` in `mkdocs.yml`. If the repository is ever renamed or moved to
another owner, update `site_url` to match, or the canonical links and the sitemap
will point at the old address.
