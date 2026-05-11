# **TELUS Digital Case Study** 

## **Purpose**

This document provides complete instructions for an LLM to transform raw source material from an operations team into a finished, single-slide TELUS Digital case study. It applies to **every service line** (CXM, Trust & Safety, AI Data Solutions, Digital Solutions, etc.) and covers **copywriting** (tone, structure, word choice) and **slide layout/design** (spatial arrangement, typography, color, metadata).

---

## **1\. Input Requirements**

The LLM will receive one of two source types, plus KPI metrics and metadata:

| Input | Description |
| :---- | :---- |
| **Source material** | EITHER an unstructured interview transcript with the ops team covering the client engagement, problems faced, what was delivered, and results achieved, OR a structured CSV / spreadsheet pulled from a Google Drive folder per `CSV Input Workflow.md`. Either source feeds the same fact set downstream. |
| **KPI metrics** | 3–4 headline performance stats with their labels (e.g. "2.3M+ / Interactions in 2024"). |
| **Metadata fields** | Industry, Channels (Voice, Chat, Email…), Languages, Delivery Geo(s), Partnership Since / Year of Delivery, Scope (if applicable). |

**Confidentiality rule:** The client must NEVER be named in the slide body or title. Reference them generically by industry and scale (e.g. "a Fortune 100 retailer", "a leading global dating platform", "a global logistics and shipping enterprise"). The real client name goes ONLY in the hidden speaker notes with the line: *"Client: \[Name\] (Please remove before sharing externally)"*.

Note for the LLM: if the source material is missing any of the initial 3 inputs (source material, KPI metrics, Metadata fields) Ask for the specific detail to the user (e.g. Do not ask for metadata fields, ask for channels)

---

## **2\. Copywriting — Structure**

Every case study follows a strict **three-act narrative** on a single slide. The sections always appear in this order:

### **2.1 Title (Headline)**

* A concise, action-oriented headline that frames the **transformation or value delivered**, not the client's problem.  
* Written in sentence case (not Title Case for every word).  
* Length: 6–12 words.  
* Does NOT name the client. Uses industry context instead.  
* Pattern: `[Verb-ing] + [what was achieved] + [for whom / in what context]`

**Examples from source material:**

* "Scaling customer support to meet seasonal surges"  
* "Driving Operational Excellence for a Global Dating Platform"  
* "Transforming delivery support for a global logistics leader"

### **2.2 Challenge (1 paragraph, 3–5 sentences)**

This section establishes **why the client needed TELUS Digital**. It must accomplish three things:

1. **Introduce the client anonymously** — Use a descriptive phrase that conveys scale and industry (e.g. "a Fortune 100 retailer with a high-volume e-commerce platform").  
2. **State the business problem** — What operational gap, scaling need, or quality issue existed.  
3. **Raise the stakes** — Why this was hard or urgent (pandemic shifts, first-time outsourcing, time-sensitive environment, etc.).

Tone: Factual, credibility-building. No hype. The complexity of the situation should speak for itself.

### **2.3 Solution (1–2 paragraphs, 4–7 sentences total)**

This is the **longest section** and the core of the case study. It explains WHAT TELUS Digital did and HOW.

Rules:

* Always starts with TELUS Digital as the subject performing an action ("TELUS Digital launched…", "TELUS Digital delivered…", "Starting with 20 agents…, TELUS Digital expanded…").  
* Describes the **operational model**: geo, team size, scope of work, channels covered.  
* Highlights **specific methodologies or tools** by name when applicable (e.g. "Fuel iX coaching assistant", "high impact data intelligence system", "rigorous QA framework").  
* Shows **progression** — from starting state to scaled state (e.g. "20 agents → 250-agent bilingual operation over five years").  
* Includes at least one concrete operational detail (workforce planning, KPI introduction, knowledge base simplification, coaching automation, etc.).  
* Can split into two paragraphs if the solution has two distinct dimensions (e.g. operational setup \+ technology enablement).

Tone: Confident and specific. Uses action verbs. Avoids vague consulting-speak. Should read like a practitioner wrote it, not a marketer.

### **2.4 Outcome (1 paragraph, 3–5 sentences)**

This section describes the **result and ongoing relationship**. It must:

1. **Quantify or qualify the impact** — reference the KPI stats contextually or summarize the transformation.  
2. **Show trust earned** — exclusive vendor status, methodology adopted globally, expanded scope, etc.  
3. **End on a forward-looking or partnership-affirming note** — demonstrate that the engagement deepened over time.

Tone: Measured pride. Not boastful. Let results speak. Often ends with an insight or a "this proves that…" framing.

### **2.5 KPI Stats Bar (3–4 metrics)**

Each metric follows this **three-line** format:

```
[Large number/percentage]          ← hero, large, FOREST green (#00804A), bold
[Short metric name]                ← black, bold, 1–3 words (e.g. "AHT", "Transfer rate")
[Comparison vs. target]            ← gray, smaller (e.g. "102.2% vs. target", "99.8% vs. target")
```

Rules:

* Use 3 or 4 metrics. Never fewer than 3, never more than 4\.  
* The number (line 1\) is the hero — bold, large, colored FOREST green (`#00804A`).  
* The metric name (line 2\) is a short fragment, 1–3 words, naming what the number measures (e.g. "AHT", "Transfer rate", "Adherence", "Repeat contact rate"). Black, bold.  
* The comparison (line 3\) anchors the number against a target or benchmark (e.g. "102.2% vs. target", "below 15% target", "out of 5"). Gray, smaller. **Omit this line if no comparison is available — do not pad it.**  
* Mix metric types when possible: volume (2.3M+ interactions), rate (14% repeat contact rate), compliance (93% net staffing), satisfaction (88% agent satisfaction).

---

## **3\. Copywriting — Tone & Voice**

### **Overall Voice**

* **Third person, past/present tense.** TELUS Digital is always the subject of the Solution section. The client is "the client" or described by their industry.  
* **Professional but not stiff.** Reads like a senior delivery leader explaining the work to a peer, not like ad copy.  
* **Specificity over generality.** Prefer "250-agent bilingual operation" over "large-scale team." Prefer "ID Verification, Safety Appeals, and Content Moderation" over "various trust and safety functions."  
* **No superlatives or unsubstantiated claims.** Don't say "world-class" or "best-in-class." If something was the highest, say "one of the highest" and tie it to a metric.

### **Words and Phrases to Use**

* "Engaged", "launched", "delivered", "expanded", "introduced", "streamlined", "combined", "equipped"  
* "Operational model", "workforce planning", "service quality", "real-time visibility", "root cause analysis"  
* "Scalable", "bilingual", "end-to-end", "time-sensitive", "high-impact"  
* "Earned the client's trust", "became the standard", "without service disruption"

### **Words and Phrases to Avoid**

* "Cutting-edge", "revolutionary", "game-changing", "synergy", "leverage" (as a verb)  
* "We are proud to…", "We are pleased to…"  
* Any first-person ("we", "our") — except in the Solution section where "we" can appear sparingly for flow (e.g. "Together, we streamlined workflows"). Even then, prefer "TELUS Digital" as the subject.  
* Client's real name anywhere in the visible slide content.

---

## **4\. Slide Layout & Design Specification**

The case study is a **single slide** in widescreen 16:9 format (13.33" × 7.5").

### **4.1 Spatial Zones**

The slide is divided into two major zones:

```
┌─────────────────────────────────┬──────────────┐
│                                 │  RIGHT       │
│  MAIN CONTENT AREA              │  SIDEBAR     │
│  (~68% width)                   │  (~32% width)│
│                                 │              │
│  Title                          │  Industry    │
│  Challenge | Outcome (2-col)    │  + icon      │
│  Solution (full width)          │  ─────────── │
│  ──────────────────────         │  Metadata    │
│  KPI Stats Bar (3-4 cols)       │  grid        │
│                                 │              │
│                                 │  ─────────── │
│                                 │  Photo       │
│                                 │  (bottom     │
│  ───────────────────────────    │   half)      │
│  TELUS Digital logo | Confid.   │              │
└─────────────────────────────────┴──────────────┘
```

### **4.2 Right Sidebar**

The right sidebar occupies approximately the rightmost 32% of the slide and has a **light gray background** (`F2F3ED`). It contains three stacked sections:

1. **Industry badge** (top): A line-art **icon to the left** of the industry name in bold (e.g. "Retail", "Technology"), with an optional sub-label below (e.g. "Online Dating Platform"). The icon is sourced from the skill's `icons/` folder — one PNG per supported industry (Automotive, Banking and FinTech, Games, Healthcare, Media, Retail, Tech, Telecomms, Travel and Hospitality). The generator reads the source PNG's **native pixel dimensions** and locks the icon's slide height to ~0.52" so the width scales proportionally; the icon must never be stretched or squashed. If the industry doesn't match any available icon, the generator falls back to a generic line-art package mark and that substitution is flagged in QA.  
2. **Metadata grid** (middle): A 2×2 or 3×2 grid of label-value pairs. Labels are uppercase, bold, small font (e.g. "CHANNELS:", "LANGUAGES:", "DELIVERY GEOS:", "PARTNERSHIP SINCE:"). Values are regular weight below or beside the label.  
3. **Photo** (bottom half): A contextual stock photo related to the industry, cropped to fill the bottom portion of the sidebar. This image bleeds to the right and bottom edges.

A thin horizontal line (green or gray) separates the industry badge from the metadata, and the metadata from the photo.

### **4.3 Main Content Area**

**Title:** Top-left, large font (\~36–44pt), set in **HN for TELUS SA Display**. Part of the title may be rendered in FOREST green (`00804A`) for emphasis (typically the action verb phrase), with the rest in black. Occupies 1–2 lines max.

**Challenge \+ Outcome:** These sit side by side in a **two-column layout** directly below the title. "Challenge" is the left column, "Outcome" is the right column. Section headers ("Challenge", "Outcome") are bold, black, \~14–16pt. Body text is regular weight, \~11–12pt, black.

**Solution:** Full width below the Challenge/Outcome row. Section header "Solution" in bold (sometimes in green). Body text same size as Challenge/Outcome. May span 1–2 paragraphs.

**KPI Stats Bar:** Anchored to the bottom of the main content area, above the footer. 3–4 metrics arranged horizontally. Each metric has:

* A left-edge vertical FOREST green bar (accent line, \~2px wide, `00804A`)  
* A large number in FOREST green (`00804A`), approximately 24–28pt  
* A short metric name below in black, bold, \~10–11pt (1–3 words)  
* An optional comparison line below the metric name, in gray, \~9pt (e.g. "102.2% vs. target")

### **4.4 Footer**

A thin bar at the very bottom of the slide. The **TELUS Digital horizontal wordmark** (`references/TELUS_Digital_EN_Hor_RGB_Blk_2025.png`, black, English) sits in the **bottom-left corner**, vertically centered inside the footer band and locked to roughly 0.26" tall so it never distorts. The word "Confidential" appears immediately to the right of the logo on the same baseline, in slate gray, 8pt. Page number sits on the far right when used in multi-slide decks.

The logo file is the canonical brand asset and must not be substituted, recolored, or stretched. If the PNG is missing from the `references/` folder, the generator falls back to a "TELUS Digital" text wordmark and a Step 8 QA check will flag the slide as non-compliant.

### **4.5 Typography**

| Element | Font | Size | Weight | Color |
| :---- | :---- | :---- | :---- | :---- |
| Slide title | HN for TELUS SA Display | 36–44pt | Regular/Bold | Black \+ FOREST green accent (`00804A`) |
| Section headers (Challenge, Solution, Outcome) | HN for TELUS SA | 14–16pt | Bold | Black (or FOREST green for Solution) |
| Body text | HN for TELUS SA | 11–12pt | Regular | Black (`222220`) |
| Metadata labels | HN for TELUS SA | 9–10pt | Bold / Uppercase | Black |
| Metadata values | HN for TELUS SA | 9–10pt | Regular | Black |
| KPI numbers | HN for TELUS SA Display | 24–28pt | Bold | FOREST green (`00804A`) |
| KPI metric name | HN for TELUS SA | 10–11pt | Bold | Black |
| KPI comparison (optional) | HN for TELUS SA | 9pt | Regular | Slate gray (`595956`) |
| Footer | HN for TELUS SA | 8pt | Regular | Gray |

### **4.6 Color Palette**

| Role | Hex | Usage |
| :---- | :---- | :---- |
| FOREST green (primary accent) | `00804A` | Title accent words, KPI numbers, stat bar accent lines, Solution section header, sidebar divider |
| TELUS green (alternate) | `66CC00` | Available as a secondary brand green if additional contrast is needed; not used on case study slides by default |
| Near-black | `222220` | Primary text color |
| Black | `000000` | Headings, bold text |
| Sidebar background | `F2F3ED` | Right sidebar fill |
| Light gray | `DEE0D9` | Divider lines in sidebar |
| White | `FCFDFB` | Main content area background |

### **4.7 Speaker Notes**

The hidden notes field must contain:

```
Client: [Real client name] (Please remove before sharing externally)
Service Line(s): [e.g. CXM, T&S]
Industry: [Full industry descriptor]
Logo Permission: No
```

Add "Ops Leads:" line only if that information is available from the transcript.

---

## **5\. Step-by-Step Process for the LLM**

Given the source material (transcript or parsed CSV) and KPIs, follow these steps:

### **Step 1 — Extract Facts from the Source Material**

Read the transcript end-to-end (or scan the parsed CSV fact set) and extract:

* What industry is the client in? What is their approximate size/scale?  
* What problem or need prompted the engagement?  
* When did the partnership start? Where is it delivered from?  
* What channels and languages are supported?  
* What did TELUS Digital build/deploy/do operationally?  
* Were any specific tools, platforms, or methodologies named (e.g. Fuel iX, QA frameworks)?  
* What was the starting team size vs. current? How did scope grow?  
* What were the measurable results?

### **Step 2 — Write the Title**

Compose a 6–12 word action-oriented headline following the pattern: `[Verb-ing] + [what was achieved] + [for/in context]`. Do not name the client.

### **Step 3 — Write the Challenge**

Write 3–5 sentences introducing the anonymous client, their business problem, and why it was difficult. Ground it in specifics from the transcript.

### **Step 4 — Write the Solution**

Write 4–7 sentences describing TELUS Digital's operational response. Start with TELUS Digital as the subject. Include geo, team composition, scope, tools, and growth trajectory. Be specific.

### **Step 5 — Write the Outcome**

Write 3–5 sentences summarizing the impact, trust earned, and forward trajectory. Reference KPIs contextually. End on a partnership-affirming note.

### **Step 6 — Format the KPI Stats**

Take the 3–4 provided metrics and write each as a number \+ short label pair. Ensure labels are under 10 words and add contextual anchors (targets, scales) where helpful.

### **Step 7 — Assemble Metadata**

Fill in: Industry, Channels, Languages, Delivery Geo(s), Partnership Since (or Year of Delivery), and Scope (if applicable). Use the exact uppercase-label format.

### **Step 8 — Draft Speaker Notes**

Include the confidential client name, service lines, industry, and logo permission status.

---

## **6\. Quality Checklist**

Before finalizing, verify:

* \[ \] Client name appears NOWHERE in visible slide content  
* \[ \] Title is action-oriented, 6–12 words, sentence case  
* \[ \] Challenge establishes anonymous client \+ problem \+ stakes (3–5 sentences)  
* \[ \] Solution leads with TELUS Digital as subject, includes specifics (4–7 sentences)  
* \[ \] Outcome shows measurable impact \+ trust earned \+ forward note (3–5 sentences)  
* \[ \] 3–4 KPI stats, each with a FOREST green hero number, a short metric name (black, bold), and (where available) a comparison-vs.-target line in gray  
* \[ \] No superlatives or unsubstantiated claims  
* \[ \] Tone is confident, practitioner-level, not marketing fluff  
* \[ \] All metadata fields populated  
* \[ \] Speaker notes include confidential client name \+ removal warning  
* \[ \] Total word count for all body text fits comfortably on a single slide (\~250–350 words across all sections)

### **6.1 — Layout QA (mandatory before delivery)**

Copy QA is not enough — the slide must also be visually clean. After rendering the `.pptx`, open the file and verify every item below. If any check fails, **do not ship the file**. Tighten the affected copy, regenerate, and re-check.

* \[ \] **Challenge / Outcome body text stays inside its column.** The bottom line of the Challenge or Outcome paragraph must not overlap or bleed into the "Solution" header or the Solution body below.  
* \[ \] **Solution body stays above the KPI stats bar.** The final line of the Solution paragraph(s) must not touch or overlap the KPI accent bars or numbers.  
* \[ \] **Title fits in one or two lines.** No wrapped third line. No clipping at the right edge. The green accent phrase renders in TELUS green; the remaining title text is black.  
* \[ \] **Sidebar metadata is fully visible.** Each label (CHANNELS, LANGUAGES, DELIVERY GEOS, PARTNERSHIP SINCE, SCOPE) and its value must be fully contained inside the sidebar, with no text clipping, no text running behind the photo, and no text bleeding off the right edge.  
* \[ \] **Industry badge is fully visible.** The line-art icon (from `icons/`) sits to the left of the industry name at its native aspect ratio — no stretching, no squashing. Industry name and optional sub-label fit above the first divider line in the sidebar. If a fallback package mark was used in place of a missing industry icon, that's noted in the delivery message.  
* \[ \] **Photo placeholder occupies the bottom portion of the sidebar.** It does not overlap metadata above or the footer below.  
* \[ \] **KPI bar renders all 3–4 metrics cleanly.** Each hero number renders in FOREST green, the metric name is black and bold, and the comparison line (where present) is gray and legible. The FOREST green accent bars align vertically. No truncated lines, no overlap between the three KPI lines.  
* \[ \] **Footer is visible.** The TELUS Digital wordmark image renders cleanly in the bottom-left corner, with "Confidential" to its right, and neither element is covered by the KPI bar above.  
* \[ \] **No shape is positioned off-slide.** All content lives within the 13.33" × 7.5" slide canvas.
* \[ \] **Typography renders correctly in HN for TELUS.** Open the `.pptx` on a machine with HN for TELUS installed. The title and KPI hero numbers must render in `HN for TELUS SA Display`; body, headers, metadata, KPI labels, and footer must render in `HN for TELUS SA`. If PowerPoint substitutes a fallback (e.g. Calibri, Arial), the visual will not match the reference — install the fonts from `fonts/` and re-open before sign-off.

### **What to do if a layout check fails**

1. Identify the overflowing section (Challenge, Outcome, or Solution are the most common culprits).  
2. Tighten that section's copy by 10–15% — reduce sentence count within the template's allowed range (Challenge 3–5, Solution 4–7, Outcome 3–5) or trim wordy phrases. Do not drop meaning or core proof points.  
3. If the sidebar is clipped, shorten metadata values (e.g., "Content Moderation, Ad Review, AI Content Review" → "Content Mod., Ad Review, AI Review") rather than removing fields.  
4. Regenerate the `.pptx` and re-run the full Layout QA checklist.  
5. Never ship a case study with visible overlap or clipping — even if the copy passes §6 structural QA.

---

## **7\. Example Output Structure**

For reference, here is the skeleton of a completed case study:

```
TITLE: [Action-verb phrase describing transformation]

SIDEBAR:
  Industry: [Industry name]
  Icon: [Relevant industry icon]
  Channels: [Voice / Chat / Email]
  Languages: [English, Spanish, etc.]
  Delivery Geos: [Country]
  Partnership Since: [Year]

CHALLENGE:
[3–5 sentences: anonymous client intro → business problem → stakes]

OUTCOME:
[3–5 sentences: results → trust earned → forward-looking close]

SOLUTION:
[4–7 sentences: what TELUS Digital did → operational model → tools → growth]

KPI STATS:
  [Number1] — [Metric name 1] — [Comparison vs. target 1]  (third line optional)
  [Number2] — [Metric name 2] — [Comparison vs. target 2]  (third line optional)
  [Number3] — [Metric name 3] — [Comparison vs. targe