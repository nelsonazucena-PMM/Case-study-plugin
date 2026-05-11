# Case Study Suite

End-to-end TELUS Digital case study tooling, bundled as a single Cowork plugin.

## What's inside

| Component | Type | Purpose |
| :---- | :---- | :---- |
| `case-study-template-finder` | Skill | Searches the shared Drive folder of interview templates and returns the right Google Form link for a given product or solution. |
| `case-study-workflow-v1` | Skill | Builds a brand-compliant single-slide case study `.pptx` from an interview transcript or a Drive CSV/XLSX. Enforces the AI Standards privacy gate, drafts copy, renders the slide, runs QA, and delivers the file. Ships with the HN for TELUS font family, industry icons, the official wordmark, and a `python-pptx` generator script. |
| `case-study-orchestrator` | Agent | Single entry point. Routes the request to the right skill — or chains both — based on whether the user needs a template, has source material to build from, or is starting from scratch. |

## How the pieces fit together

```
                    ┌──────────────────────────────┐
   User request ───►│  case-study-orchestrator     │
                    │  (agent — classifies intent) │
                    └──────────────┬───────────────┘
                                   │
                ┌──────────────────┼──────────────────┐
                ▼                                     ▼
   case-study-template-finder           case-study-workflow-v1
   (Drive folder → Google Form link)    (transcript → .pptx slide)
```

- **Template only** → orchestrator calls `case-study-template-finder` and stops.
- **Build only** → orchestrator calls `case-study-workflow-v1` directly.
- **Full pipeline** → orchestrator runs the template finder first, waits for the user to return with completed interview material, then runs the workflow.

## Requirements

- Google Drive connector enabled in the user's Cowork session (both skills depend on Drive — the finder for template lookup, the workflow for the live AI Standards file and the optional CSV/XLSX input branch).
- Python 3 with `python-pptx` for the slide renderer. The workflow skill will install it on demand if missing.
- The HN for TELUS font family installed on whatever machine opens the rendered `.pptx`. The `.otf` files ship inside the workflow skill at `skills/case-study-workflow-v1/fonts/`.
- Optional: the `anti-ai-slop-writing` skill installed in the same Cowork environment. The workflow's Step 6.5 polish pass uses it if available and degrades gracefully if not.

## Author

Nelson Azucena · nelson.azucena@telusinternational.com
