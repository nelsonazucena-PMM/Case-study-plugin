---
name: case-study-template-finder
description: >
  Find and retrieve case study interview templates from the team's Google Drive folder.
  Use this skill whenever the user asks for a case study template, interview template,
  CS template, or mentions needing a template for a specific product or solution.
  Trigger on phrases like "find me a case study template", "I need the interview template
  for CXM", "which templates do we have", "case study template for [product]",
  "get the CS template", "interview form for [solution]", or any request to browse,
  search, or locate templates in the Interview Templates folder. Even casual mentions
  like "template for that product" or "where's the interview form" should trigger this skill.
  Also trigger when a user names a specific TELUS Digital product and asks for a template,
  form, or interview guide related to it.
---

# Case Study Template Finder

This skill searches a shared Google Drive folder containing case study interview templates — one per product/solution — and helps the user find and open the right one.

## Source folder

All templates live in a single Google Drive folder:

- **Folder name**: Interview Templates
- **Folder ID**: `1rbcynQHEwdt1m-jaXsthSbyftTgDJQ_r`
- **Direct link**: https://drive.google.com/drive/folders/1rbcynQHEwdt1m-jaXsthSbyftTgDJQ_r

## How to find templates

Use the Google Drive `search_files` tool with a `parentId` query to list files in the folder. Always load this tool first via `tool_search` if it isn't already available.

### Step 1: Check if the user already named a product

If the user's message includes a specific product or solution name (e.g. "CXM", "Trust & Safety", "AI Data Solutions"), skip straight to a targeted search:

```
query: parentId = '1rbcynQHEwdt1m-jaXsthSbyftTgDJQ_r' and title contains '<product keyword>'
```

If that returns a match, provide the link and you're done (skip to Step 3).

If it returns nothing, try alternate keywords or shorter terms (e.g. if "Content Moderation" fails, try "CM" or "Moderation"). Be creative with abbreviations and synonyms — template names may use shorthand.

If still nothing is found after a couple of attempts, fall through to Step 2 so the user can browse.

### Step 2: Browse mode — show recent templates

Fetch all templates in the folder sorted by recency:

```
query: parentId = '1rbcynQHEwdt1m-jaXsthSbyftTgDJQ_r'
pageSize: 50
```

Present the **3 most recently modified** templates as selectable options using `ask_user_input`, plus a fourth write-in option so the user can type a product name to search for:

```
Options:
1. [Template title A]
2. [Template title B]
3. [Template title C]
4. "Search for a different product..."
```

Use `single_select` type. The option labels should be the template titles as they appear in Drive (clean and readable).

If the user picks option 4 (the search option), ask them what product they're looking for — then go back to Step 1 with their answer as the search term.

If the folder has 3 or fewer templates total, show all of them and still include the search option as the last row (it serves as a fallback if they don't see what they need).

### Step 3: Deliver the result

Once the user has selected or you've found the right template, provide:

1. The **template title**
2. The **direct link** to open it (use the `viewUrl` from the search results)

Keep it simple — just the name and a clickable link. No need to read the file contents.

### Fallback: folder link

If no matching template is found after searching, tell the user you couldn't find an exact match and give them the direct link to browse the folder themselves:

> I couldn't find a template matching that product. You can browse all available templates here: https://drive.google.com/drive/folders/1rbcynQHEwdt1m-jaXsthSbyftTgDJQ_r

## Important notes

- Templates are Google Forms (not Docs or Sheets), so the `viewUrl` will point to a Google Form.
- The folder will grow over time — always fetch fresh results from Drive rather than relying on any cached list.
- Template titles follow the pattern `[Product] Interview - CS template` but this isn't guaranteed, so search by keyword rather than assuming a rigid naming convention.
- If the Google Drive tool call fails or returns an error, provide the folder link as a graceful fallback and suggest the user check access permissions.
