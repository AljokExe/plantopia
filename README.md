# 🌿 Plantopia

This repository manages my Notion [Plant Care Tracker](https://app.notion.com/p/Plant-Care-Tracker-0c4de382842683f5855001cc01f5263a).

## Add a plant

Run `/add-plant` in Claude Code from this repo, with any details you have:

```
/add-plant Monstera deliciosa, got it today, bright indirect light, photo ~/Pictures/monstera.jpg
```

The skill asks for the missing fields, proposes care values, and creates the page in the Plants database.

## Plant folders

Each plant has a folder in `plants/` with the same layout. Copy `plants/_template/` for a new plant.

```
plants/<plant-slug>/
├── plant.md      # facts and care values, each with a source; mirrors the Notion properties
├── journal.md    # dated entries, newest first; mirrors the toggles on the Notion page
└── photos/       # YYYY-MM-DD_<plant-slug>[_<label>].jpeg
```

Photo names start with the ISO date, so they sort by date. Add a short label when one day has more than one photo, for example `2026-10-10_ficus-ginseng_roots.jpeg`.

| Plant | Notion |
| --- | --- |
| [Ficus Ginseng](plants/ficus-ginseng/plant.md) | [page](https://app.notion.com/p/3f5de38284268168bcb2f3883ea394cb) |

## Notion structure

| Database | Purpose | Data source |
| --- | --- | --- |
| Plants | One page per plant: care needs, ownership, last watering | `collection://8c6de382-8426-83a7-a439-879214aa5bf8` |
| Green Thumb Guide | Care articles, tagged by growth stage, problem and genus | `collection://dcade382-8426-8280-a8da-076c15371053` |
| Supplies | Owned supplies and the shopping list | `collection://f1ade382-8426-836a-af85-87bf2fb4ba2e` |

### Plants properties

- **Name** (title)
- **Ownership** (status): Wishlist, Currently Owned, Previously Owned
- **Humidity Requirement** (multi-select): Humidity tolerant, 50%, 80%, Ventilation
- **Lighting Requirement** (multi-select): Full Sun, Partial Shade, 5000k, 8000k
- **Temperature Requirement** (multi-select): 5℃, 10℃, 15℃, 18℃, 30℃, 35℃
- **Soil Type** (multi-select): Excellent Drainage, Slightly Acid, Allow soil to dry between waterings
- **Special Care Instructions** (text)
- **Watered Date** (date) and **Waterd** (button that sets the date)

### Green Thumb Guide properties

- **Name** (title), **URL** (url)
- **Tags** (multi-select): growth stages (Seedling, Cutting, Budding, Flowering, Fruiting, Dormant), problems (Pest Infestation, Disease, Stressed) and genera (Anthurium, Alocasia, Begonia, Rex Begonia, Cane type Begonia)

### Supplies properties

- **Name** (title), **URL** (url)
- **Status** (status): Shopping List, Owned
- **Tag** (select): Pest and Disease Control, Soil Substrates, Gardening Tools, Fertilizers
