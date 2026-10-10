---
name: add-plant
description: Add a new plant to the Notion Plant Care Tracker (Plants database). Use when the user wants to add, record or register a plant they own, got, or want (wishlist). Accepts free-form plant details as arguments.
argument-hint: "[plant name and any details: care needs, date you got it, photo paths, notes]"
---

# Add a plant to the Plant Care Tracker

Create one page in the **Plants** database that looks like the template plants
(for example "Alocasia Polly" and "Begonia 'Sierra Mountain King'"). Do not
change or delete the template plants. They stay as examples.

- Data source: `collection://8c6de382-8426-83a7-a439-879214aa5bf8`
- Page template (adds the "Watered" button to the page body): `ddfde382842683d79d260120df324e63`

## 1. Collect the data

Parse `$ARGUMENTS` first. Then ask for the missing fields in ONE message.
Do not ask for a field that the arguments already give. Only **Name** is required.

| Field | Notion property | Values |
| --- | --- | --- |
| Name | `Name` (title) | Common or cultivar name, for example `Monstera deliciosa` or `Begonia 'Autumn Ember'`. Plain text, no Markdown. |
| Ownership | `Ownership` (status) | `Currently Owned` (default), `Wishlist`, `Previously Owned` |
| Light | `Lighting Requirement` (multi) | `Full Sun`, `Partial Shade`, `5000k`, `8000k` (the last two are grow-light colour temperatures) |
| Temperature range | `Temperature Requirement` (multi) | Two values: the minimum and the maximum, for example `10℃` + `30℃`. Options: `5℃`, `10℃`, `15℃`, `30℃`, `35℃` |
| Humidity | `Humidity Requirement` (multi) | `Humidity tolerant`, `50%`, `80%`, `Ventilation` |
| Soil | `Soil Type` (multi) | `Excellent Drainage`, `Slightly Acid`, `Allow soil to dry between waterings` |
| Care tip | `Special Care Instructions` (text) | One or two sentences, the single most useful tip for this plant |
| Last watered | `Watered Date` (date) | ISO date. Omit if unknown |
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

**Option values.** Use the existing options above. When a needed value does not
exist (for example a minimum of `18℃`), tell the user. Add it only after the
user agrees. A new option in a create call adds the option to the database.

For a `Wishlist` plant, ask only for Name, a cover URL and an optional note.
The template wishlist plants have no care data and no journal.

## 2. Create the page

Call `notion-create-pages` with parent `{"data_source_id": "8c6de382-8426-83a7-a439-879214aa5bf8"}`,
`template_id: "ddfde382842683d79d260120df324e63"`, the properties, and the
`cover` URL if you have one. Do not pass `content`: the template supplies the body.

Property formats:
- Multi-select: a JSON array string, for example `"[\"10℃\",\"30℃\"]"`.
- Date: `"date:Watered Date:start": "2026-10-10"` and `"date:Watered Date:is_datetime": 0`.
- Do not set `Waterd`. It is a button, and the API cannot set it.

The template applies asynchronously. Fetch the new page until its content shows
the button block (`<unknown ... alt="button"/>`). Retry a few times before you
add content. Content added too early ends up above or without the button.

## 3. Add the body

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
   Later entries go directly below the button and above older entries.

## 4. Photos

- **Journal photos** (local files): call `notion-create-file-upload` with the
  file name. Then POST the file once with curl:
  `curl -sS -X POST "<upload_url>" <-H for each upload_headers entry> -F "file=@<path>"`.
  Use the returned `markdown_source` in the toggle. Limit: 20 MiB per file.
- **Cover photo**: the API sets a cover only from a public URL. If the user has
  only a local photo, upload it to the journal, then tell the user to set it as
  the cover in Notion (Change cover → Upload). The gallery views show the
  cover, so a plant without a cover looks empty there.

## 5. Green Thumb Guide (optional)

Check the genus against the `Tags` options of the Green Thumb Guide
(`collection://dcade382-8426-8280-a8da-076c15371053`): Anthurium, Alocasia,
Begonia, Rex Begonia, Cane type Begonia. If the genus is new, offer to add one
care-guide article (Name = article title, `userDefined:URL`, `Tags` = the genus
plus the relevant stage or problem tags). Add the new genus tag only after the
user agrees.

## 6. Report

Give the page link and a short list of what you set. Name every field that
you left empty and every value that you proposed.
