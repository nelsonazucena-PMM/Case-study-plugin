# Drive Folder Input Workflow

Alternate input-handling guide for the case study skill. Use this file when the source material is a CSV (`.csv`) or Excel (`.xlsx`) file stored in a Google Drive folder, rather than an uploaded interview transcript.

Follow Steps A–D below in order, then hand control back to `SKILL.md` at **Step 2 — Privacy gate**.

## When this workflow applies

Trigger this file the moment the user signals their source material lives in a Drive folder as a CSV or XLSX. Common phrasings:

- "I have a CSV with the case info"
- "The case is in an xlsx in Drive"
- "Pull it from this Drive folder instead"
- "I have a file in Drive, not a transcript"

If the user uploaded a transcript directly to the chat, ignore this file and stay on `SKILL.md` Step 1.

## Step A — Capture the Drive folder

The merged Step 1 prompt in `SKILL.md` already invites the user to paste a Drive folder URL alongside the transcript option. In most runs the URL will already be in the conversation by the time this file is opened.

1. **If the URL is already provided.** Extract the folder ID from the pasted text. Accept either a `https://drive.google.com/drive/folders/<id>` URL or a raw folder ID. Strip query parameters. Proceed straight to Step B — do not re-ask.
2. **If the user only signaled a CSV / XLSX without pasting a URL** (e.g., "I have a CSV in Drive" with no link), ask once in plain chat — not `AskUserQuestion` — because the folder URL is free-form text. Use wording like:

   > *"Paste the full Google Drive folder URL (or just the folder ID) and I'll pull the file list from there."*

   Wait for the paste, then extract the folder ID as above.

Never assume a default folder. The folder location is always user-supplied, per run.

## Step B — List every recent file in the folder

Once you have the folder ID, list the files inside it. Do not filter by mime type or extension — surface everything the folder contains.

1. Call the Google Drive connector's `search_files` tool scoped to the folder ID with **no mime-type filter**. If `search_files` is unavailable, fall back to `list_recent_files` and filter only by the folder ID in the parent field.
2. Sort the returned files by `modifiedTime` descending (most recently modified first).
3. Do not drop or hide anything based on file type. Spreadsheets, docs, PDFs, images, transcripts, slides — all of it is fair game and goes through to Step C.
4. If zero files are found, post a one-line note and ask the user to either re-share the folder, paste a different folder URL, or upload a source file directly. Do not proceed to Step C until at least one file is found.
5. If the connector errors out (not connected, permissions, folder not found), halt and tell the user briefly what failed. Ask whether to retry, switch connectors, or fall back to an uploaded transcript per `SKILL.md` Step 1. Do not silently default.

## Step C — Ask which file to use

Confirm the file via a single `AskUserQuestion` call.

- Surface **up to three** candidate filenames as the first three options, in order of most recently modified.
- Add a **fourth option that is intentionally blank** — leave its label empty so the user has an explicit free-text slot to type the filename of any other file in the folder that didn't make the recent-three cut.
- Do not tag, badge, or otherwise annotate the options with file type. Show the filenames as they appear in Drive.

If the folder has fewer than three files, list the ones that exist and still leave the blank fourth option available.

Carry the chosen file ID forward as `source_file_id` for Step D.

## Step D — Read and parse the file

Branch on the file type captured in Step C:

- **CSV.** Call the Drive connector's `read_file_content` tool with `source_file_id`. Parse the returned text with Python's `csv` module (or `pandas.read_csv`) into rows and columns.
- **XLSX.** Call the Drive connector's `download_file_content` tool with `source_file_id` to pull the binary. Open it with `openpyxl` (or `pandas.read_excel`) and read the first worksheet into rows and columns.

The data will typically take one of three shapes — detect which one before extracting:

- **Long form (key/value).** Each row is one fact; columns are `Field` and `Value` (or similar). Map each `Value` cell to its `Field` label.
- **Wide form.** Each column is a field; the first data row holds the case content. Read the header row for field names, then pull values from row 2.
- **Multi-case.** Each row is a separate case study. If the file has more than one data row, surface up to four row identifiers (client name, ID, or row index) via `AskUserQuestion` and let the user pick which row to build the case study from.

Map the cells into the same fact set that `SKILL.md` Step 3 expects:

- Client industry and approximate scale or size
- Business problem or need that prompted the engagement
- Partnership start year
- Delivery geography / geos
- Channels supported (Voice, Chat, Email, etc.)
- Languages supported
- What TELUS Digital built, deployed, or operated
- Named tools, platforms, or methodologies
- Starting team size to current team size; scope evolution
- Measurable results (KPIs, adoption, trust signals)
- Service line(s) involved

If a field is empty in the file, do not invent it. Flag the gap so `SKILL.md` Step 5 (missing-KPI / metadata ask) can pick it up.

## Return to SKILL.md

Once Steps A–D are complete, the parsed CSV / XLSX data replaces the transcript as the source material for the rest of the run. Resume at **`SKILL.md` Step 2 — Privacy gate**.

From this point forward every instruction in `SKILL.md` applies as written, including:

- **Step 2** — scan the parsed facts for the prohibited categories listed in AI Standards §1 (customer PII, employee data, credentials, confidential legal documents, trade secrets). The privacy gate is identical regardless of input source.
- **Step 2.5** — confirm service line and client name from the file-derived facts.
- **Step 2.6** — compendium lookup.
- **Steps 3 through 9** — unchanged.

Do not re-run the transcript-upload prompt from Step 1. The CSV / XLSX has already replaced it as the source material.
