---
name: add-plant
description: Add a new plant to the Notion Plant Care Tracker (Plants database). Use when the user wants to add, record or register a plant they own, got, or want (wishlist). Accepts free-form plant details as arguments.
argument-hint: "[plant name and any details: care needs, date you got it, photo paths, notes]"
---

# Add a plant to the Plant Care Tracker

Create one page in the **Plants** database that follows the pattern of
"Ficus Ginseng" (`plants/ficus-ginseng/` and its Notion page): botanical
callout and a growth journal of dated toggles.

- Data source: `collection://8c6de382-8426-83a7-a439-879214aa5bf8`

## 1. Collect the data

Parse `$ARGUMENTS` first. Then ask for the missing fields in ONE message.
Do not ask for a field that the arguments already give. Only **Name** is required.

| Field | Notion property | Values |
| --- | --- | --- |
| Name | `Name` (title) | Common or cultivar name, for example `Monstera deliciosa` or `Begonia 'Autumn Ember'`. Plain text, no Markdown. |
| Ownership | `Ownership` (status) | `Currently Owned` (default), `Wishlist`, `Previously Owned` |
| Light | `Lighting Requirement` (multi) | `Full Sun`, `Partial Shade`, `5000k`, `8000k` (the last two are grow-light colour temperatures) |
| Temperature range | `Temperature Requirement` (multi) | Two values: the minimum and the maximum, for example `10℃` + `30℃`. Options: `-20℃`, `5℃`, `10℃`, `15℃`, `18℃`, `23℃`, `30℃`, `35℃` |
| Humidity | `Humidity Requirement` (multi) | `Humidity tolerant`, `50%`, `80%`, `Ventilation` |
| Soil | `Soil Type` (multi) | `Excellent Drainage`, `Slightly Acid`, `Allow soil to dry between waterings` |
| Care tip | `Special Care Instructions` (text) | One or two sentences, the single most useful tip for this plant |
| Botanical note | callout in page body | Scientific name or parentage, for example `Begonia 'Sierra Mountain King' = Begonia maculata x Begonia lubbersii` |
| Cover photo | page cover | A public image URL (see section 4) |
| First journal entry | toggle in page body | Date the user got the plant, short notes, local photo paths |

If the user does not know the care needs, propose values, and say that they
are proposals. Show the full proposal and get a "yes" before you create the page.

**Evidence rule.** Back every proposed value, care tip and botanical note with
a source that you opened (WebSearch, then WebFetch on the page). Prefer
university extension services, the RHS and botanical gardens. Treat shop care
sheets and forums as weak support only. Put the source link next to each value
in the proposal. Mark a value "unverified" when no source supports it, and say
when sources disagree.

**Option values.** Use the existing options. Fetch the data source first, because
options change (for example `18℃` was added for the Ficus Ginseng). When a
needed value does not exist, tell the user, and add it only after the user
agrees. A create call with an unknown option fails: add the option first with
`notion-update-data-source`, `ALTER COLUMN "<property>" SET MULTI_SELECT(...)`,
and list every existing option with its colour, or Notion deletes the missing
ones. Then update the options list in README.md.

For a `Wishlist` plant, ask only for Name, a cover URL and an optional note.
A wishlist plant has no care data and no journal.

## 2. Create the plant folder

Copy `plants/_template/` to `plants/<plant-slug>/` (lowercase, hyphens, for
example `ficus-ginseng`). Fill `plant.md` with the approved values and their
sources. Move the user's photos into `photos/` and rename them
`YYYY-MM-DD_<plant-slug>[_<label>].<ext>`. Ask for the date when the file name
or EXIF data does not give it. Read each photo, and note in the journal only
what is visible. Write `journal.md` entries newest first.

## 3. Create the page

Call `notion-create-pages` with parent `{"data_source_id": "8c6de382-8426-83a7-a439-879214aa5bf8"}`,
the properties, and the `cover` URL if you have one. Do not pass `template_id`:
the database's default template is blank.

Property formats:
- Multi-select: a JSON array string, for example `"[\"10℃\",\"30℃\"]"`.

## 4. Add the body

Read `notion://docs/enhanced-markdown-spec` once (with `notion-fetch`) before
you write content. Then use `notion-update-page`:

1. **Botanical note** (if given): `insert_content` with `position: {"type":"start"}`:
   ```
   <callout icon="/icons/leaf_gray.svg">
   	<scientific name or parentage>
   </callout>
   ```
2. **First journal entry** (if given): `insert_content` at the end. The journal is
   a list of toggle headings, newest first, each followed by a divider. The
   heading is `<event> YYYY/MM/DD`, in a personal tone. An emoji is fine:
   ```
   ### Welcome home 🪴 2026/10/10 {toggle="true"}
   	<notes from the user, in their words>
   	![](<markdown_source of each photo>)
   ---
   ```
   Later entries go directly below the botanical note and above older entries.

## 5. Photos

- **Journal photos** (local files): call `notion-create-file-upload` with the
  file name. Then POST the file once with curl:
  `curl -sS -X POST "<upload_url>" <-H for each upload_headers entry> -F "file=@<path>"`.
  Use the returned `markdown_source` in the toggle. Limit: 20 MiB per file.
- **Cover photo**: the API sets a cover only from a public URL. For a local
  photo, use Claude in Chrome on the plant page: click "Add cover", hover the
  cover, click "Change", open the "Upload" tab. Notion creates its file input
  only on a click, and a real click opens a native dialog. So first patch
  `HTMLInputElement.prototype.click` (javascript_tool) to append a `type=file`
  input to the DOM instead of opening the dialog, then click "Upload file" in
  JS, find the input, and attach the photo with `file_upload`. Restore the
  original `click` afterwards. Use the newest photo. The gallery views show the
  cover, so a plant without a cover looks empty there.

## 6. Green Thumb Guide (optional)

Check the genus against the `Tags` options of the Green Thumb Guide
(`collection://dcade382-8426-8280-a8da-076c15371053`). Fetch the data source
for the current options. If the genus has no article, offer to add one
care-guide article (Name = article title, `userDefined:URL`, `Tags` = the genus
plus the relevant stage or problem tags). Add the new genus tag only after the
user agrees.

## 7. Report

Add the Notion link to `plant.md` and to the plant table in README.md.
Commit the plant folder. Give the page link and a short list of what you set. Name every field that
you left empty and every value that you proposed.
