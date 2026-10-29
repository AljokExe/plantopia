# 🌿 Plantopia

Plantopia keeps a Notion plant tracker and a Git folder per plant in step. Claude Code skills do the work: add a plant, log a watering, write a journal entry.

This copy manages my Notion [Plant Care Tracker](https://app.notion.com/p/Plant-Care-Tracker-0c4de382842683f5855001cc01f5263a). Follow the steps below to set up your own.

## What you get

- **A Notion page** with three databases: Plants, Green Thumb Guide and Supplies. Each plant page has a "Watered" button, a botanical note and a growth journal of dated toggles.
- **A folder per plant** in `plants/`, with care values and their sources, a journal and photos.
- **Claude Code skills** that write to Notion and to the plant folders at the same time.

## Set up your own copy

### 1. Get the tools

- A [Notion](https://www.notion.so) account.
- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview).
- The Notion MCP server in Claude Code. Use the Notion connector on claude.ai, or add it in a terminal:

  ```sh
  claude mcp add --transport http notion https://mcp.notion.com/mcp
  ```

  Then run `/mcp` in Claude Code and log in to Notion.
- Optional: [Claude in Chrome](https://claude.com/chrome). The skills use it to upload a local photo as a page cover. Without it, you set covers by hand.

### 2. Duplicate the Notion template

Open the [Plant Care Tracker template](TEMPLATE_LINK) and click **Duplicate**. Notion copies the page, the three databases and the plant page template into your workspace.

### 3. Get this repository

Fork or clone this repository, and open a terminal in it:

```sh
git clone https://github.com/<you>/plantopia.git
cd plantopia
claude
```

### 4. Connect the repository to your Notion page

Copy the link of your duplicated page (**Share** → **Copy link**). Then run:

```
/setup-tracker https://www.notion.so/<your-page-link>
```

The skill finds your databases and your plant page template, and writes their IDs to `plantopia.json`. It compares your schema with the one in this README. It also asks if you want to remove my plant folders from `plants/`.

## Skills

Run these in Claude Code from this repository. Give any details you have; the skill asks for the rest.

| Skill | What it does | Example |
| --- | --- | --- |
| `/setup-tracker` | Connects the repository to your Notion page. Run it once. | `/setup-tracker https://www.notion.so/...` |
| `/add-plant` | Creates the Notion page and the plant folder. Proposes care values, each with a source. | `/add-plant Monstera deliciosa, got it today, photo ~/Pictures/monstera.jpg` |
| `/water` | Sets "Watered Date" on one or more plants. | `/water ficus, nepenthes` |
| `/journal` | Adds one dated entry to `journal.md` and the same toggle to the Notion page. | `/journal nepenthes new pitcher today, photo ~/Pictures/pitcher.jpg` |

## Configuration

`plantopia.json` holds the Notion IDs. The skills read it, so you do not edit the skills.

| Key | Value |
| --- | --- |
| `notionPage` | Link to the Plant Care Tracker page |
| `dataSources.plants`, `.greenThumbGuide`, `.supplies` | `collection://` data source URL of each database |
| `plantPageTemplate` | ID of the Plants database template that adds the "Watered" button |

## Plant folders

Each plant has a folder in `plants/` with the same layout. `/add-plant` copies `plants/_template/` for a new plant.

```
plants/<plant-slug>/
├── plant.md      # facts and care values, each with a source; mirrors the Notion properties
├── journal.md    # dated entries, newest first; mirrors the toggles on the Notion page
└── photos/       # YYYY-MM-DD_<plant-slug>[_<label>].jpeg
```

Photo names start with the ISO date, so they sort by date. Add a short label when one day has more than one photo, for example `2026-10-10_ficus-ginseng_roots.jpeg`.

| Plant | Notion |
| --- | --- |
| [Brighamia insignis](plants/brighamia-insignis/plant.md) | [page](https://app.notion.com/p/3f5de382842681929e96dd7524536799) |
| [Ficus Ginseng](plants/ficus-ginseng/plant.md) | [page](https://app.notion.com/p/3f5de38284268168bcb2f3883ea394cb) |
| [Nepenthes](plants/nepenthes/plant.md) | [page](https://app.notion.com/p/3f5de382842681ea8770feedb886a3ea) |

## Notion structure

| Database | Purpose | Key in `plantopia.json` |
| --- | --- | --- |
| Plants | One page per plant: care needs, ownership, last watering | `dataSources.plants` |
| Green Thumb Guide | Care articles, tagged by growth stage, problem and genus | `dataSources.greenThumbGuide` |
| Supplies | Owned supplies and the shopping list | `dataSources.supplies` |

When you change a schema in Notion, update this section in the same commit. `/setup-tracker` compares your databases with it.

### Plants properties

- **Name** (title)
- **Ownership** (status): Wishlist, Currently Owned, Previously Owned
- **Humidity Requirement** (multi-select): Humidity tolerant, 50%, 80%, Ventilation
- **Lighting Requirement** (multi-select): Full Sun, Partial Shade, 5000k, 8000k
- **Temperature Requirement** (multi-select): 5℃, 10℃, 15℃, 18℃, 23℃, 30℃, 35℃
- **Soil Type** (multi-select): Excellent Drainage, Slightly Acid, Allow soil to dry between waterings
- **Special Care Instructions** (text)
- **Watered Date** (date) and **Waterd** (button that sets the date). The API cannot press the button, so the skills set "Watered Date".

### Green Thumb Guide properties

- **Name** (title), **URL** (url)
- **Tags** (multi-select): growth stages (Seedling, Cutting, Budding, Flowering, Fruiting, Dormant), problems (Pest Infestation, Disease, Stressed) and genera (Anthurium, Alocasia, Begonia, Rex Begonia, Cane type Begonia)

### Supplies properties

- **Name** (title), **URL** (url)
- **Status** (status): Shopping List, Owned
- **Tag** (select): Pest and Disease Control, Soil Substrates, Gardening Tools, Fertilizers

## License

[CC BY-NC 4.0](LICENSE). You may use, change and share this repository for any non-commercial purpose. Give credit to the original.
