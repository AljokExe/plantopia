# Plantopia

This repo manages the Notion page "Plant Care Tracker". See README.md for the database IDs and schemas.

- Use the Notion MCP tools to read and write the databases. Use the `collection://` data source URLs from README.md.
- When you change a database schema in Notion, update README.md in the same change.
- The databases hold only our own data. The template example rows were moved to the Notion trash on 2026-10-10.
- Each plant has a folder in `plants/` (layout in README.md). Keep `journal.md` and the Notion toggles in step: one entry each, same date and title.
- Add plants with the `/add-plant` skill (`.claude/skills/add-plant/SKILL.md`). It holds the page pattern: botanical callout and a growth journal of dated toggle headings, newest first.
