# Plantopia

This repo manages the Notion page "Plant Care Tracker". See README.md for the database IDs and schemas.

- Use the Notion MCP tools to read and write the databases. Use the `collection://` data source URLs from README.md.
- The Plants property "Waterd" is a button. You cannot set it through the API. Set "Watered Date" instead.
- When you change a database schema in Notion, update README.md in the same change.
- The template rows (9 plants, 13 guide articles) stay as examples. Do not edit or delete them.
- Add plants with the `/add-plant` skill (`.claude/skills/add-plant/SKILL.md`). It holds the page pattern: template button, botanical callout, and a growth journal of dated toggle headings, newest first.
