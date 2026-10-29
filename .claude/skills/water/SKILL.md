---
name: water
description: Log a watering in the Notion Plant Care Tracker. Sets "Watered Date" on one or more plants. Use when the user says they watered a plant, some plants, or all plants.
argument-hint: "[plant names or 'all'] [optional date, default today]"
---

# Log a watering

Set **Watered Date** on each plant that the user watered. Do not set
**Waterd**: it is a button, and the API cannot press it.

## 1. Find the plants

Parse `$ARGUMENTS` for plant names, `all`, and an optional date. Use today's
date when no date is given. Convert "yesterday" and weekday names to an ISO date.

Each `plants/<slug>/plant.md` has a "Notion page" link. Match the names
against the folder slugs and the `# <Plant name>` headings. Accept partial and
case-insensitive matches, for example "ficus" for "Ficus Ginseng".

- `all` means every plant whose `plant.md` says `Ownership: Currently Owned`.
- If a name matches more than one plant, ask which one.
- If a name matches no folder, search the Plants data source (`dataSources.plants`
  in `plantopia.json`) with `notion-search`. Tell the user that the plant has
  no folder.

Do not ask for a confirmation when every name has exactly one match.

## 2. Set the date

For each plant, call `notion-update-page` on its page with:

```json
{
  "date:Watered Date:start": "YYYY-MM-DD",
  "date:Watered Date:is_datetime": 0
}
```

Do not write a journal entry for a normal watering. If the user adds notes
about the plant (new leaf, pests, repot), offer `/journal` for the notes.

## 3. Report

List each plant with its new date and its Notion link. Name each plant that
failed and give the error.
