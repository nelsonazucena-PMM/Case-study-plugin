---
name: case-study-workflow-v1
description: Use this for case study creation
---

# Case Study Builder

## Purpose

Produce a single-slide TELUS Digital case study (.pptx) from one of two source inputs: an unstructured interview transcript with the operations team, or a structured CSV (`.csv`) or Excel (`.xlsx`) file pulled from a Google Drive folder (see `references/Drive Folder Input Workflow.md`). The skill enforces the full TELUS Digital Case Study Template (structure, copy rules, layout, typography, KPI bar) and keeps every output brand-compliant with the TELUS Digital AI Standards (privacy gate, brand naming, voice, persona-mapped pain points).

**Terminology note.** Throughout the steps below, "the transcript" is used as shorthand for the source material. When the source is a CSV or XLSX instead, every instruction that says "scan the transcript" or "extract from the transcript" applies identically to the fact set parsed in `references/Drive Folder Input Workflow.md` Step D.

## Reference files — read these before doing any work

This skill depends on two non-negotiable source documents. **One is a live Google Drive file that must be fetched fresh at the start of every run; one is a local file bundled with the skill.** Read both fully before drafting.

### 1. AI Standards (LIVE — Google Drive)

The TELUS Digital brand compliance document. Governs privacy, naming, voice, numbers/dates, formatting, persona pain points, and design palette. Always enforced.

This is the single source of truth for brand compliance. It is maintained in Google Drive and is updated over time, so the skill **must always fetch the live copy at the start of a run** — never rely on a cached version.

- **Drive URL:** https://drive.google.com/file/d/1uobDkBvDzzN0Z5mEmLmbcCq7N9TGXiOP/view?usp=drive_link
- **File ID:** `1uobDkBvDzzN0Z5mEmLmbcCq7N9TGXiOP`

**How to fetch (do this before Step 2 runs):**

1. Call the Google Drive connector's `read_file_content` tool with the file ID above (`1uobDkBvDzzN0Z5mEmLmbcCq7N9TGXiOP`). This returns the current markdown contents of the AI Standards document.
2. Treat the returned markdown as the source of truth for every subsequent AI Standards reference in this skill (e.g., §1 privacy gate, §2.1 personas, §3 naming, §4 voice, §5 numbers, §7 formatting, §8.1–§8.3 pain points, §9 colors).

**If the fetch fails** (Drive connector unavailable, permissions error, file not found, etc.):

- Halt the workflow.
- Tell the user the live AI Standards file cannot be reached and briefly state why (e.g., "the Google Drive connector isn't connected in this session").
- Ask the user whether to retry or abort. Brand compliance in this skill is gated on having the current live standards.

### 2. Case Study Template (LOCAL)

`references/Case Study Template.md` — structural and copy template for the case study (applies to every service line, not CXM only). Defines Title/Challenge/Solution/Outcome/KPI structure, slide layout, quality checklist. This file stays local inside the skill directory; read it with the normal file tools.

### 3. TELUS Digital logo (LOCAL)

`references/TELUS_Digital_EN_Hor_RGB_Blk_2025.png` — the official TELUS Digital horizontal wordmark (English, RGB, black). The generator places this image in the **bottom-left corner of the slide** as the footer brand mark. The asset lives inside the skill directory so the script can resolve it by relative path; do not move or rename it. If the file is missing the generator falls back to a text wordmark and Step 8 QA will flag the issue.

### 4. Industry icons (LOCAL)

`icons/` — a folder of black line-art PNGs, one per supported industry, used as the **industry badge in the right sidebar** (placed to the left of the industry name).

Available files (filename → match against the `industry` field, case-insensitive substring):

| Industry value (examples) | Icon file |
| :---- | :---- |
| Automotive | `icons/Automotive.png` |
| Banking, FinTech, Financial Services | `icons/Banking and FinTech.png` |
| Games, Gaming | `icons/Games.png` |
| Healthcare, Health, Pharma | `icons/Healthcare.png` |
| Media, Entertainment, Streaming | `icons/Media.png` |
| Retail, E-commerce | `icons/Retail.png` |
| Tech, Technology, SaaS, Software | `icons/Tech.png` |
| Telecomms, Telecom, Telco | `icons/Telecomms.png` |
| Travel, Hospitality, Airlines, Hotels | `icons/Travel and Hospitality.png` |

**Sizing rule — read the original image's dimensions before placing.** The PNGs are not all the same shape (some run ~1888 × 2220, others ~2122 × 2016). The generator must read each file's native pixel dimensions, lock the icon's slide height to the sidebar slot (~0.52"), and let the width scale proportionally so the icon never stretches or squashes. `python-pptx` preserves aspect ratio automatically when only one dimension is specified — pass `height=Inches(0.52)` and omit `width`. Do **not** hard-code a width.

**Canonical industry labels — required for the `industry` JSON field.** The script's icon matcher uses case-insensitive substring scanning, which means a compound string like `"Comms and Media"` would match `media` first and pick the wrong icon (Media instead of Telecomms). To prevent that, the LLM **must snap the `industry` field in the JSON payload to one of these nine canonical labels**:

- Automotive
- Banking and FinTech
- Games
- Healthcare
- Media
- Retail
- Tech
- Telecomms
- Travel and Hospitality

Anything more nuanced — sub-vertical, scale, business model — goes in the `industry_sublabel` field instead (e.g., `"industry_sublabel": "Online dating platform"` or `"Fortune 100 e-commerce"`). This is the **only** safe way to feed the script. Confirmation of the canonical label happens in Step 4 (see that step for the AskUserQuestion pattern).

**No-match fallback.** If the confirmed industry still doesn't map to any file in the table above (rare — only happens when the user picks "Other" and types a label outside the canonical nine), the generator falls back to the built-in line-art package icon and the slide still ships. Step 8 QA notes the substitution so the user can decide whether to add a new icon to `icons/`.

### 5. Drive Folder Input Workflow (LOCAL)

`references/Drive Folder Input Workflow.md` — alternate input-handling guide for runs where the case material is a CSV (`.csv`) or Excel (`.xlsx`) file in a Google Drive folder rather than an uploaded transcript. Read this file in full when the user signals their source is a CSV or XLSX in Drive. It covers the Drive folder prompt, file selection, parsing, and the handoff back to Step 2 (Privacy gate).

### Order of operations

Fetch and read the live AI Standards first (brand compliance lens), then read `Case Study Template.md` (structural lens). When the two conflict, AI Standards Section 1 (privacy) and Section 3 (naming/spelling) override everything. The task-specific template overrides AI Standards defaults on tone, length, and structure only when explicitly specified.

## When to use this skill

Trigger whenever the user wants any of the following:

- A single-slide case study, client story, win story, or success story
- A TELUS Digital "leave-behind" case study for any service line (CXM, T&S, AI Data, Digital Solutions, etc.)
- To convert an operations interview transcript into a polished client narrative
- A slide that pairs with a sales pitch to prove delivery capability

## Workflow overview

```
1.  Ask the user for the source material              (BLOCKING)
    (transcript upload OR Drive folder URL; CSV/XLSX branch
     defers to references/Drive Folder Input Workflow.md)
2.  Privacy gate scan                                 (HARD HALT if triggered)
2.5 Confirm service line and client name
2.6 Check the service line compendium for existing
    case studies on this client                       (non-blocking)
3.  Extract facts from transcript or parsed CSV/XLSX
4.  Infer persona + industry → confirm with user
    (service line is already known from Step 2.5)
5.  Ask for any missing KPIs / metadata
6.  Draft copy (Title, Challenge, Solution, Outcome, KPIs)
6.5 Anti-slop polish pass on Challenge/Solution/Outcome
    (invoke anti-ai-slop-writing skill; skip gracefully if not installed
     and flag the skip in the Step 9a delivery message)
7.  Render single-slide .pptx using scripts/generate_case_study.py
8.  Run the QA checklist (Template §6 + AI Standards compliance)
9a. Deliver the .pptx to the user's CASE STUDY folder
    (+ skip-note if the Step 6.5 polish pass was unavailable)
```

## Step 1 — Request the source material (do this FIRST, before anything else)

Before reading the reference files, before doing any analysis, before extracting any facts — **always** ask the user to provide the source material if it has not already been provided in this conversation. The skill supports two input sources: an uploaded interview transcript, or a CSV (`.csv`) or Excel (`.xlsx`) file stored in a Google Drive folder.

Ask in plain chat (not `AskUserQuestion`) with language like:

> *"Before we start, please upload the transcript from the operations team's interview. If you don't have a transcript, please provide the URL of the Drive folder where you have your case studies located."*

### If the user uploads a transcript

Accept `.txt`, `.docx`, `.pdf`, or `.md`, either attached to the chat or pasted inline. Do not proceed to Step 2 until the transcript is actually present in the conversation (either as an uploaded file in `/sessions/focused-loving-ramanujan/mnt/uploads/` or pasted inline). If the user says "I'll paste it in chat instead," wait for the paste.

### If the user provides a Drive folder URL

Open `references/Drive Folder Input Workflow.md` and follow its Steps A–D end-to-end. That file handles the Drive folder confirmation, lists the `.csv` and `.xlsx` files inside it, confirms which one to use via `AskUserQuestion`, and parses the data into the same fact set Step 3 expects. When it completes it hands control back here at Step 2.

## Step 2 — Privacy gate (AI Standards §1)

The moment the transcript arrives, scan it for any of the prohibited inputs defined in the live AI Standards document (Drive file ID `1uobDkBvDzzN0Z5mEmLmbcCq7N9TGXiOP`, fetched per the Reference files section above) Section 1:

- Customer PII (names, emails, phone numbers, addresses of end users)
- Employee personal data (HR records, salaries, performance reviews)
- Credentials (passwords, logins, security keys)
- Confidential legal documents
- Trade secrets (unreleased product plans)

**Note on client name:** the client's company name IS allowed in the transcript — it will only be stored in hidden speaker notes and kept out of the visible slide content per Template §1. What is NOT allowed is PII belonging to that client's end-customers or employees.

If any prohibited category is found, **halt immediately** and return exactly:

> *"Operations will not execute due to sensitive information protection."*

Then tell the user which category was detected (without quoting the sensitive value) and ask them to redact and re-upload.

If the transcript is clean, proceed.

## Step 2.5 — Confirm service line and client name

Before extracting facts or inferring anything else, lock in two foundational pieces of metadata: the **service line** the case study belongs to, and the **client name**. These two values drive Step 2.6 (compendium lookup) and downstream context, so capture them here in a single `AskUserQuestion` call.

### Infer first

- **Service line** — read the transcript for direct cues. Customer support / CX operations → **CXM**. Content moderation, safety appeals, ID verification → **Trust & Safety**. Data labeling, model training data, annotation → **AI Data Solutions**. Digital build, automation, integration → **Digital Solutions**.
- **Client name** — most transcripts name the client directly somewhere. Pull the first plausible candidate (e.g., "we partnered with Acme Corp on…" → `Acme Corp`). If the transcript only refers to "the client" or uses an internal codename, leave the inferred value blank and let the user type it.

### Then confirm via `AskUserQuestion`

Make a single `AskUserQuestion` call with two questions:

**Question 1 — Service line.** Use these four options, with the inferred value first and labeled "(Recommended)":

- CXM (Customer Experience Management)
- Trust & Safety
- AI Data Solutions
- Digital Solutions

**Question 2 — Client name.** Surface the inferred client name as the first option labeled "(Recommended — confirm)" so the user can one-click accept it. Add 1–2 alternate candidates if the transcript mentions other companies that could plausibly be the client. The auto-appended "Other" lets the user type a different name. Always include this clarifying note in the question's description field:

> *"The client name will be anonymized in the visible slide content (Template §1). It is only stored in the hidden speaker notes for internal reference, and used in Step 2.6 to check whether this client is already covered in the service line compendium."*

If the transcript named no client at all, skip the recommended option and let the user type the name via "Other" — but make the question text explicit: *"The transcript didn't name the client. Please type their name."*

Carry both confirmed values forward as session values: `service_line` and `client_name`. They feed Step 2.6 immediately and Step 8 (speaker notes) later.

## Step 2.6 — Check service line compendium for existing client case studies

To avoid duplicating coverage, check whether this client already has a case study in the TELUS Digital compendium for the chosen service line. This is a **non-blocking** check — if matches exist, surface them to the user and ask whether to proceed; if the connector is unavailable, skip with a brief note and continue.

### Compendium mapping

| Service line | Drive file ID | Compendium URL |
| :---- | :---- | :---- |
| CXM | `1lBUvNqlF01Y9yqFGlypj1NYQouJws4_U37EQ3UW-Ah4` | https://docs.google.com/presentation/d/1lBUvNqlF01Y9yqFGlypj1NYQouJws4_U37EQ3UW-Ah4/edit |
| Trust & Safety | `1PWH1TAueFRraTS5zr7JlDSLQCagMRWg_UtYQmWsoT04` | https://docs.google.com/presentation/d/1PWH1TAueFRraTS5zr7JlDSLQCagMRWg_UtYQmWsoT04/edit |
| AI Data Solutions | `12ZXg2q9tG9mQDlcj5xyywOC_D4CRCJMl0WKngCAwHAs` | https://docs.google.com/presentation/d/12ZXg2q9tG9mQDlcj5xyywOC_D4CRCJMl0WKngCAwHAs/edit |
| Digital Solutions | *(no compendium published yet)* | n/a |

### Process

1. **Digital Solutions branch.** If the user picked Digital Solutions in Step 2.5, post a one-line note to the user (*"No Digital Solutions compendium configured yet — skipping the prior-coverage check."*) and proceed directly to Step 3. Do not call any connector.
2. **Fetch the compendium.** For the other three service lines, call the Google Drive connector's `read_file_content` tool with the matching file ID from the table above.
3. **Search for the client.** Run a case-insensitive scan of the returned text for the confirmed `client_name`. The compendiums consistently mark each case study with a `Client: [Name]` line (often followed by `(Please remove before sharing externally)`). Prioritize matches on those lines, but also surface any other mention of the client name in the body.
4. **Identify slide locations.** Each case study in the compendium has a slide number rendered as a standalone integer (typically near the start or end of the entry). Capture every slide number associated with a hit. If a slide number cannot be confidently identified, fall back to the surrounding section header or case study title as the locator.
5. **Construct slide URLs when possible.** Google Slides deep-links use the format `<compendium URL>#slide=id.<slide-id>`. The slide IDs are not always recoverable from the connector output, so prefer slide-deep-links only when the ID is clearly present in the fetched content. If not, fall back to the compendium URL plus the slide number(s).
6. **Reply to the user.**
   - **If matches found:** list each slide number (and slide-deep-link if available), give the compendium URL, summarize the existing coverage in one short sentence per match (e.g., *"Slide 4 — `Driving operational excellence for a global dating platform` (2019)"*), and ask via `AskUserQuestion` whether to **proceed with a new case study**, **stop and reuse the existing one**, or **proceed but flag the overlap in the speaker notes**.
   - **If no matches:** post a single-sentence confirmation (*"No existing case studies for [Client] in the [Service Line] compendium — proceeding."*) and continue to Step 3.
7. **Connector failure.** If the Drive connector is unavailable, returns an error, or the file ID is inaccessible, post a one-line note (*"Compendium check skipped — couldn't reach the [Service Line] compendium in this session."*) and continue to Step 3. Do not halt.

The result of this step does not change the case study draft itself — it gives the operator a chance to avoid redundant work or to consciously build a complementary version.

## Step 3 — Extract facts from the transcript

Read the transcript once end-to-end, then extract the following (from Template §5.1):

- Client industry and approximate scale/size
- Business problem or need that prompted the engagement
- Partnership start year
- Delivery geography/geos
- Channels supported (Voice, Chat, Email, etc.)
- Languages supported
- What TELUS Digital built, deployed, or operated
- Named tools, platforms, or methodologies (Fuel iX, QA frameworks, workforce planning, etc.)
- Starting team size → current team size; scope evolution
- Measurable results (KPIs, adoption, trust signals)
- Service line(s) involved (CXM, Trust & Safety, AI Data Solutions, Digital Solutions, etc.)

If a fact is not present in the transcript, do not make one up. Flag it as missing for Step 5.

## Step 4 — Infer persona and industry, then confirm

The service line was already captured in Step 2.5, so this step focuses on **persona** and **industry**. The skill should **infer then confirm** for both.

### Infer first

From the transcript, guess the most likely target persona and industry:

- **Persona inference** — look at the problem framing and results language. If the transcript emphasizes cost, workforce, resilience, seasonal peaks → likely **COO**. CX strategy, NPS/CSAT, loyalty → likely **CCO**. Agent KPIs, SLAs, retention → likely **VP/Director Contact Center Ops**. Conversion, revenue, upsell → likely **VP Revenue/Sales**. Platform, AI tooling, integration → likely **CIO/CTO**. See the live AI Standards document §2.1 and §2.2 for the full persona list (fetched per the Reference files section above).
- **Industry inference** — snap whatever the transcript says into one of the **nine canonical icon labels** listed in Reference files §4 (Automotive, Banking and FinTech, Games, Healthcare, Media, Retail, Tech, Telecomms, Travel and Hospitality). Map common synonyms to the canonical label: "Insurance" / "Payments" / "Wealth" → **Banking and FinTech**; "Gaming" / "eSports" → **Games**; "Pharma" / "Medtech" / "Hospital" → **Healthcare**; "Streaming" / "Publishing" / "Entertainment" → **Media**; "E-commerce" / "Marketplace" → **Retail**; "SaaS" / "Software" / "Platform" → **Tech**; "Telco" / "Cable" / "ISP" → **Telecomms**; "Hotels" / "Airlines" / "Cruise" → **Travel and Hospitality**.

### Ambiguity rule for compound industries

If the transcript describes a hybrid like **"Comms and Media"**, **"Fintech and Banking"**, or **"Travel + Tech"**, do NOT pass the compound string to the script — the icon matcher uses substring scanning and the wrong icon would win. Instead, surface both candidate canonical labels in the AskUserQuestion confirmation below and let the user pick one. The picked label is what goes into the `industry` JSON field; the original nuanced phrasing goes into `industry_sublabel`.

### Then confirm via `AskUserQuestion`

Ask both in a single `AskUserQuestion` call (two questions). Put the inferred answer first with "(Recommended)" on its label. Use these persona options drawn from AI Standards §2.1–§2.2:

- CCO (Chief Customer Officer)
- COO (Chief Operating Officer)
- VP / Director Contact Center Ops
- VP Revenue / Sales
- CIO / CTO
- CDO (Chief Digital Officer)
- Quality Head
- Learning Management Leader
- Procurement Manager

Cap the list at 4 options (including the inferred one at the top). The user can always pick "Other" for anything not listed.

For industry, offer the inferred canonical label first with "(Recommended)", then up to three other plausible canonical labels from the nine. Examples:

- Inferred Telecomms: `Telecomms (Recommended)`, `Media`, `Tech`
- Inferred Banking and FinTech: `Banking and FinTech (Recommended)`, `Tech`, `Retail`
- Inferred Travel and Hospitality: `Travel and Hospitality (Recommended)`, `Retail`, `Media`

The user can always pick "Other" and type a custom label. If they do, warn them that the icon will fall back to the generic line-art package mark and give them the option to either accept the fallback or pick one of the canonical nine. Whatever the user picks becomes the `industry` field in the JSON payload; their original phrasing (or any additional descriptor like "Online dating platform" or "Fortune 100 e-commerce") goes into `industry_sublabel`.

### Why this matters

The persona answer drives which pain points from AI Standards §8 are emphasized in the Challenge section and which messaging emphasis (from §2.1) leads the Outcome section:

- **COO / VP Contact Center Ops** → pull from §8.1 Operational challenges; lead with cost-efficiency and scalability proof points
- **CCO / VP Revenue** → pull from §8.2 Customer experience gaps; lead with strategic outcomes and LTV
- **CIO / CTO / CDO** → pull from §8.3 Strategic transformation needs; lead with platform integration and AI enablement

## Step 5 — Ask for any missing KPIs or metadata

Per Template §1, the three required inputs are (a) interview transcript, (b) 3–4 KPI metrics, (c) metadata (industry, channels, languages, delivery geo, partnership since, scope). If the transcript didn't surface all of them:

- Ask the user **specifically** for what's missing — not a generic "metadata please."
- **Always use `AskUserQuestion` for every clarifying question, even for free-text answers** (delivery geo, languages, KPI specifics, dates). This is a UX preference: clicking is easier than typing. For open-ended fields, offer 2–4 inferred options drawn from the transcript; "Other" is automatically appended by the tool and lets the user type free text. Examples:
  - *"What's the delivery geography?"* → options: "Multi-site", "Romania", "Philippines", "Guatemala" (+ Other)
  - *"What languages are supported?"* → options: "English only", "English + Spanish", "English + multilingual" (+ Other)
  - *"Here are the KPIs drafted from the transcript. Keep or swap?"* → options: "Keep these 3", "Keep these 4", "Swap KPI #4 for the alternate" (+ Other)
- You must have **3 or 4 KPI number+label pairs** before drafting. Never fewer than 3, never more than 4 (Template §2.5).
- Batch related questions into a single `AskUserQuestion` call (up to 4 questions per call) rather than asking them one at a time.

The only exception to the AskUserQuestion rule is Step 1 (requesting the transcript upload) — that must be in plain chat because `AskUserQuestion` can't prompt for a file attachment.

Do not proceed to drafting until all required inputs are gathered.

## Step 6 — Draft the copy

Follow Template §2 for structure and §3 for tone. Simultaneously apply AI Standards:

- **§3 Naming:** TELUS always fully capitalized. TELUS Digital always spelled out. Use canonical spelling list (chatbot, cybersecurity, email, omnichannel, start-up, touchpoint, well-being, Wi-Fi, etc.). Use "%" not "percent". Use "and" in body sentences, not "&".
- **§4 Voice:** Active voice, Grade 8 reading, conversational-confident. Third person. TELUS Digital is the subject of the Solution section. "Team member" preferred over "employee" when referring to TELUS Digital staff.
- **§5 Numbers:** Spell out zero–nine; numerals for 10+. Dates: "May 2021" (no comma). "More than" preferred over "over". "to" for ranges (never en dash).
- **§7 Formatting:** Headlines in sentence case. Avoid the Oxford comma in simple series. Avoid unnecessary capitalization.
- **§8 Pain points:** The Challenge section must emphasize pain points that map to the confirmed persona. Pull them verbatim or paraphrased from the appropriate §8 sub-table.

### Section-by-section rules

- **Title:** 6–12 words, sentence case, action-oriented. Pattern: `[Verb-ing] + [what was achieved] + [for/in context]`. No client name.
- **Challenge:** 3–5 sentences. Anonymous client intro (industry + scale) → business problem → stakes. Pain points should echo the persona's key focus from AI Standards §2.1 / §8.
- **Solution:** 4–7 sentences, longest section. TELUS Digital as subject. Include operational model (geo, team size, scope, channels), named tools/methodologies, and progression from starting state to scaled state. May split into two paragraphs for operational + technology dimensions.
- **Outcome:** 3–5 sentences. Quantified impact → trust earned → forward-looking close.
- **KPI Stats Bar:** 3 or 4 metrics. **Three-line format per metric**: (1) the hero number in FOREST green, bold (e.g. "406 sec", "11.9%", "94.8%"); (2) a short metric name in black, bold, 1–3 words (e.g. "AHT", "Transfer rate", "Adherence"); (3) an optional comparison line in gray (e.g. "102.2% vs. target", "below 15% target"). Omit the third line if no target or benchmark is available — do not pad it. Mix metric types (volume, rate, compliance, satisfaction) where possible.

### Hard rules from Template §3

- Never use first person except sparingly in the Solution for flow.
- Never name the client in any visible slide content. The real name goes only in speaker notes.
- Never use: "cutting-edge", "revolutionary", "game-changing", "synergy", "leverage" (as a verb), "world-class", "best-in-class", "we are proud to", "we are pleased to".

## Step 6.5 — Anti-slop polish pass (attempt before render)

After the Challenge, Solution, and Outcome copy is drafted in Step 6, run those three sections through the `anti-ai-slop-writing` skill to strip formulaic LLM patterns, filler transitions, and sentences that could apply to any context.

### How to invoke

Call the Skill tool with `skill: "anti-ai-slop-writing"`. Feed it the drafted Challenge, Solution, and Outcome paragraphs. Apply its heuristics to each — remove 20–30% of filler words and transitions, replace at least one abstract statement with a concrete example from the transcript, cut any sentence that reads as universally applicable, vary sentence length, avoid "not X but Y" structures, overused em dashes, and forced lists. Use the polished output as the final copy fed into Step 7.

### What gets polished vs. what stays untouched

| Section | Run through polish pass? |
| :---- | :---- |
| Title | No — 6–12 words, too tight to rewrite |
| Challenge | Yes — full paragraph |
| Solution | Yes — full paragraph(s); highest-value section |
| Outcome | Yes — full paragraph |
| KPI numbers / labels / context | No — fixed format, no prose |
| Metadata fields | No — fixed format |
| Speaker notes | No — internal only |

### Constraints the polish pass MUST respect

The rewrite cannot break any brand rule already enforced in Step 6. Specifically, the polished copy still needs to:

- Stay within Template §2 sentence-count bounds (Challenge 3–5, Solution 4–7, Outcome 3–5)
- Keep the client anonymous — no real client name anywhere in visible content
- Keep TELUS Digital as the subject of the Solution section
- Honor AI Standards §3 naming/spelling, §4 voice (third person, active, Grade 8), §5 numbers/dates, §7 formatting
- Keep Challenge pain points aligned with the confirmed persona (§8.1 / §8.2 / §8.3)
- Avoid the banned phrases from Step 6 ("cutting-edge", "revolutionary", "game-changing", "synergy", "leverage" as a verb, "world-class", "best-in-class", "we are proud/pleased to")

If a polish-pass edit would violate any of the above, discard that specific edit and keep the original sentence.

### Graceful degradation — skill not installed

If the `anti-ai-slop-writing` skill is not available in the user's Cowork environment (the Skill tool returns an error, the skill is not listed in available skills, or the invocation fails for any reason), do NOT halt the workflow. Instead:

1. Proceed to Step 7 using the unpolished Step 6 drafts as-is.
2. Set the session value `polish_pass_executed = false`.
3. In Step 9a, include the skip-note described there so the user knows why the polish pass was omitted.

If the skill DID run successfully, set `polish_pass_executed = true` and omit the note in Step 9a.

## Step 7 — Render the single-slide .pptx

Use the generator script shipped with this skill: `scripts/generate_case_study.py`.

The generator takes a JSON input with the drafted copy + metadata and produces a print-ready single-slide .pptx that matches the Template §4 layout (main content area ~68%, sidebar ~32%, KPI stats bar, footer). Brand styling is built in (colors from AI Standards §9 — FOREST green `#00804A` for title accent / KPI numbers / KPI accent bars / Solution header, MOONSTONE `#F3F3ED` sidebar, OBSIDIAN `#222220` text; typography is **HN for TELUS** — the Stylistic Alternates cut per AI Standards §9.4 — with `HN for TELUS SA Display` for the slide title and KPI hero numbers and `HN for TELUS SA` for everything else).

The footer also embeds the TELUS Digital wordmark image from `references/TELUS_Digital_EN_Hor_RGB_Blk_2025.png` in the bottom-left corner of the slide, sized to fit inside the footer band. "Confidential" sits to the right of the logo on the same baseline. The script resolves the image by relative path, so the `references/` folder must remain alongside `scripts/` for the render to pick it up.

The right sidebar's industry badge pulls a PNG from the `icons/` folder (see Reference files §4 for the mapping). The script matches the confirmed industry against the filename list, reads the source PNG's native pixel dimensions, and inserts it at a fixed slide height (~0.52") with width scaling automatically to preserve the icon's original aspect ratio. If no match is found, the script falls back to the built-in line-art package icon.

**Font prerequisite:** the generator writes the font names into the `.pptx` by reference, so the target machine that opens the slide needs HN for TELUS installed. The skill ships the `.otf` files in the `fonts/` folder — install them once on your machine (macOS: double-click each file → "Install Font"; Windows: right-click → Install for all users) before opening the generated deck. If the font isn't installed, PowerPoint will silently substitute a fallback and the visual will not match the reference.

### How to invoke

1. Assemble a JSON payload matching the schema described in the script's docstring. **The `industry` field must be one of the nine canonical labels** (see Reference files §4) — the value confirmed by the user in Step 4. Put any nuanced descriptor in `industry_sublabel`, never in `industry`.
2. Save it as `input.json` in the working directory.
3. Run: `python3 "/path/to/case-study-builder/scripts/generate_case_study.py" input.json "/output/path/filename.pptx"`

The script is self-contained (only `python-pptx` is required). If `python-pptx` is missing, install with `pip install python-pptx --break-system-packages`.

### Output file naming

Save to the user's selected folder at the root. Naming convention:

`<Client-Anonymous-Industry-Label>_CaseStudy_v1.pptx`

For example: `Fortune100-Retailer_CaseStudy_v1.pptx`. Increment the version number if a file with that name already exists.

## Step 8 — Final QA

Before delivering, run **three** checklists in order. All must pass. If any item fails, fix it and regenerate before moving on.

### Order of QA passes

1. **Layout QA** — open the rendered `.pptx` and verify the visual spec in `references/Case Study Template.md` §6.1. This comes first because a visually broken slide is unshippable regardless of copy quality.
2. **Structural QA** — Template §6 (copy structure and word counts).
3. **Brand compliance QA** — AI Standards (naming, voice, numbers, formatting, persona mapping).

### Layout QA details (see Template §6.1 for the full checklist)

After running the generator, inspect the output to confirm:

- No body text overlaps a section header (the Challenge/Outcome column text must NOT bleed into the Solution header)
- No body text overlaps the KPI stats bar
- Sidebar metadata is fully contained — no clipping, no text running off the edge
- Title fits in one or two lines at the specified font size
- Solution paragraphs do not spill past their block into the KPI bar

If any overlap is detected, **iterate on the copy before regenerating**:

1. Identify the overflowing section (Challenge, Outcome, or Solution)
2. Tighten that section by 10–15% — reduce sentence count within the template's allowed range (Challenge 3–5, Solution 4–7, Outcome 3–5) or trim wordy phrases
3. Rerun the generator
4. Re-check for overlap

Never ship a case study with visible text overlap.

### From Template §6 structural QA (copy)

- [ ] Client name appears NOWHERE in visible slide content
- [ ] Title is action-oriented, 6–12 words, sentence case
- [ ] Challenge: 3–5 sentences, anonymous client + problem + stakes
- [ ] Solution: 4–7 sentences, TELUS Digital as subject, specifics included
- [ ] Outcome: 3–5 sentences, measurable impact + trust earned + forward note
- [ ] 3–4 KPI stats with large number + short label
- [ ] No superlatives or unsubstantiated claims
- [ ] Tone is confident and practitioner-level
- [ ] All metadata fields populated
- [ ] Speaker notes include confidential client name + removal warning
- [ ] Total body word count fits a single slide (~250–350 words)

### From AI Standards (brand compliance QA)

- [ ] Privacy gate passed — no PII/credentials/confidential material leaked into output
- [ ] TELUS always fully capitalized; TELUS Digital always spelled out
- [ ] Canonical spellings correct (email, chatbot, omnichannel, touchpoint, Wi-Fi, start-up, well-being)
- [ ] Active voice, Grade 8 readability
- [ ] Third person throughout; no "we are proud / pleased to"
- [ ] Numbers: spelled out 0–9, numerals 10+, "%" symbol used, "to" for ranges, no ordinal date suffixes
- [ ] Oxford comma avoided in simple series
- [ ] Sentence case used for title
- [ ] "Team member" not "employee" when referring to TELUS Digital staff
- [ ] Pain points in Challenge section map to the confirmed persona (§8.1 / §8.2 / §8.3)
- [ ] Brand colors used: FOREST green `#00804A` for title accent, KPI hero numbers, KPI accent bars, and Solution section header; MOONSTONE/PEARL for sidebar; OBSIDIAN for body text; Slate gray for KPI comparison lines
- [ ] Typography is HN for TELUS SA throughout: `HN for TELUS SA Display` for slide title and KPI hero numbers, `HN for TELUS SA` for everything else. If opening the `.pptx` shows a fallback font (e.g., Calibri, Arial), install the fonts from the `fonts/` folder and re-open before shipping
- [ ] TELUS Digital logo (`references/TELUS_Digital_EN_Hor_RGB_Blk_2025.png`) renders in the bottom-left corner of the slide, vertically centered in the footer band, with no clipping and no overlap with the KPI bar above or the "Confidential" mark to its right
- [ ] Industry icon from `icons/` renders to the **left of the industry name** in the right sidebar at the natural aspect ratio of the source PNG (no stretching, no squashing). If the industry didn't match an available icon and the fallback package mark was used instead, that substitution is noted in the delivery message so the user can add a new icon if needed

If any item fails, fix and regenerate before delivering.

## Step 9 — Deliver to the user, then ask for approval

### 9a — Deliver the .pptx

Save the final .pptx to the user's currently selected Cowork folder (the CASE STUDY folder, or wherever `request_cowork_directory` points). Reply with a concise summary (one or two sentences) + a `computer://` link to the file. Do not over-explain the content — the user can open the slide themselves.

Example delivery message (polish pass ran successfully — no note needed):

> *Case study ready. [View your case study](computer:///sessions/focused-loving-ramanujan/mnt/CASE%20STUDY/Fortune100-Retailer_CaseStudy_v1.pptx)*

**Conditional skip-note.** If `polish_pass_executed == false` (the `anti-ai-slop-writing` skill was not available — see Step 6.5), append a one-line note to the delivery so the user knows the humanization layer was omitted and why. Use this wording or a close variant:

> *Case study ready. [View your case study](computer://...path...)*
>
> *Note: the `anti-ai-slop-writing` skill is not installed in your Cowork environment, so the polish pass on the Challenge, Solution, and Outcome copy was skipped. The text was finalized using only the standard template rules. Install that skill if you want the humanization pass on future runs.*

Only include the note when the polish pass was actually skipped. If the pass ran, omit the note entirely — don't mention it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    