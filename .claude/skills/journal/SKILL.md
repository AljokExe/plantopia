---
name: journal
description: Add a growth journal entry for a plant. Writes the entry to plants/<slug>/journal.md and the same dated toggle to the plant's Notion page. Use when the user reports an event for a plant they own (new leaf, repot, flower, pest, move) or adds a photo of it.
argument-hint: "[plant name] [what happened] [optional date and photo paths]"
---

# Add a journal entry

One event gives one entry in `journal.md` and one toggle on the Notion page.
Both have the same date and the same title.

## 1. Collect the data

Parse `$ARGUMENTS`. Find the plant as in `/water` (match against
`plants/<slug>/`). Ask in ONE message for what is missing:

- **Plant** (required).
- **Date**: default today. For a photo, use the date in the file name or the
  EXIF data, and ask when neither gives it.
- **Title**: a short event name, for example "First pitcher 🌱". Propose one
  from the notes.
- **Notes**: the user's words. Do not add care advice. If the user asks for
  advice, back each claim with a source that you opened.
- **Photos**: optional local paths.

## 2. Photos

Copy each photo to `plants/<slug>/photos/` as
`YYYY-MM-DD_<slug>[_<label>].<ext>`. Do not overwrite an existing file: add a
label. Read each photo, and add to the notes only what is visible.

## 3. Write journal.md

Insert the entry above the newest entry, below the header text:

```markdown
## YYYY-MM-DD: <title>

<notes>

![](photos/YYYY-MM-DD_<slug>.jpeg)
```

## 4. Write the Notion toggle

Read `notion://docs/enhanced-markdown-spec` once (with `notion-fetch`). Fetch
the plant page (link in `plant.md`).

Upload each photo as in `/add-plant` section 5 ("Journal photos"). Then insert
the toggle with `notion-update-page` `insert_content`, directly below the
button block and above the newest toggle. Check the tool schema for the
position form that targets a block.

```
### <title> YYYY/MM/DD {toggle="true"}
	<notes>
	![](<markdown_source of each photo>)
---
```

Fetch the page again and check the order: button, new toggle, older toggles.

## 5. Report

Commit the plant folder. Give the Notion link and the entry title and date.
