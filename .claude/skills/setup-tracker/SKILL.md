---
name: setup-tracker
description: Connect this repository to the user's own copy of the Notion Plant Care Tracker. Use when the user duplicated the template, forked the repo, or wants to point the skills at a different Notion page. Writes plantopia.json.
argument-hint: "[link to your duplicated Plant Care Tracker page]"
---

# Connect the repository to a Plant Care Tracker

The other skills read the Notion IDs from `plantopia.json`. This skill finds
the IDs in the user's copy of the page and writes them there.

## 1. Check the Notion connection

Call a Notion MCP tool (for example `notion-fetch` on the page link). If no
Notion tools exist or the call fails with an auth error, stop. Tell the user to
add the Notion MCP server (see "Get the tools" in README.md) and run `/mcp`.

## 2. Get the page

Take the page link from `$ARGUMENTS`. If there is no link, ask for it: in
Notion, **Share** → **Copy link** on the duplicated "Plant Care Tracker" page.

Fetch the page with `notion-fetch`. Find the three child databases by name:
**Plants**, **Green Thumb Guide** and **Supplies**. Fetch each database and
note its `collection://` data source URL. If a database is missing or has a
different name, show what the page contains and ask the user which one to use.

## 3. Find the plant page template

The Plants database has a page template that adds the "Watered" button to the
page body. `/add-plant` needs its ID.

Look for the template in the Plants data source fetch result. If the result
does not list templates, ask the user to copy the template link: open the
Plants database, click the arrow next to **New**, click **•••** next to the
template, then **Copy link**. The ID is the 32 hex characters at the end of
the link.

## 4. Compare the schema

Compare the properties and options of each data source with "Notion structure"
in README.md. Report each difference: a missing property, an other type, a
missing or extra option. Do not change the Notion schema. If the user wants
the README to match their schema, update README.md and say so.

## 5. Write plantopia.json

Show the values, then write `plantopia.json`:

```json
{
  "notionPage": "<page link>",
  "dataSources": {
    "plants": "collection://...",
    "greenThumbGuide": "collection://...",
    "supplies": "collection://..."
  },
  "plantPageTemplate": "<32 hex characters>"
}
```

## 6. Remove the old data (ask first)

**Notion rows.** Search each data source for rows (`notion-search` with the
data source URL). If rows exist, list them and ask the user if they want to
keep them. The user deletes unwanted rows in Notion. Do not delete pages
yourself.

**Plant folders.** The folders in `plants/` (all except `_template/`) belong to
the person who wrote this repository. Ask the user if they want to remove them.
On a yes:

1. Delete each `plants/<slug>/` folder except `plants/_template/`.
2. Remove all rows from the plant table under "Plant folders" in README.md.
   Keep the header row.
3. Replace the Notion link in the README intro with the user's page link.

Do nothing to the folders on a no.

## 7. Report

Give the values in `plantopia.json`, the schema differences, and what you
removed. Say that the user can now run `/add-plant`. Say that Claude in Chrome
is optional, and that it lets `/add-plant` upload a local cover photo.
