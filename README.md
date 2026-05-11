# Case Study Marketplace

Cowork plugin marketplace hosting the **case-study-suite** plugin for TELUS Digital.

## What's in this repo

```
case-study-marketplace/
├── .claude-plugin/
│   └── marketplace.json          ← marketplace manifest read by Cowork
├── plugins/
│   └── case-study-suite/          ← the plugin itself
│       ├── .claude-plugin/plugin.json
│       ├── skills/
│       │   ├── case-study-template-finder/
│       │   └── case-study-workflow-v1/
│       ├── agents/
│       │   └── case-study-orchestrator.md
│       └── README.md
└── README.md                      ← this file
```

## How to publish

1. Push this repo to GitHub (public or private — both work).
2. Cowork org admin: **Organization settings → Plugins → Add plugin → GitHub** → enter `owner/repo`.
3. Teammates: **Customize → Browse plugins → Install** `case-study-suite`.

## How to ship updates

1. Edit files under `plugins/case-study-suite/`.
2. Bump the `version` field in **both** `plugins/case-study-suite/.claude-plugin/plugin.json` **and** `.claude-plugin/marketplace.json`.
3. Commit and push to GitHub. Teammates' Cowork instances pick up the new version automatically.

## What the plugin does

- **`case-study-template-finder`** — Drive lookup for the right interview template by product or solution.
- **`case-study-workflow-v1`** — Builds a brand-compliant single-slide case study `.pptx` from a transcript or Drive CSV/XLSX.
- **`case-study-orchestrator`** (agent) — Single entry point that routes the request to the right skill or chains both.

## Maintainer

Nelson Azucena · nelson.azucena@telusinternational.com
