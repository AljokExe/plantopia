# Plantopia

This repo manages the Notion page "Plant Care Tracker". `plantopia.json` holds the page link and database IDs. README.md holds the schemas.

- Use the Notion MCP tools to read and write the databases. Use the `collection://` data source URLs from `plantopia.json`. If the file is missing or wrong, run `/setup-tracker`.
- The Plants property "Waterd" is a button. You cannot set it through the API. Set "Watered Date" instead.
- When you change a database schema in Notion, update README.md in the same change.
- The databases hold only our own data. Do not delete Notion pages yourself: list them and let the user delete them.
- Each plant has a folder in `plants/` (layout in README.md). Keep `journal.md` and the Notion toggles in step: one entry each, same date and title.
- Use the skills: `/setup-tracker`, `/add-plant`, `/water`, `/journal`. Add plants with the `/add-plant` skill (`.claude/skills/add-plant/SKILL.md`). It holds the page pattern: template button, botanical callout, and a growth journal of dated toggle headings, newest first.
