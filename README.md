# obsidian-travel-vault-example

## Summary

A privacy-safe example of an Obsidian travel-planning vault. It demonstrates structured
place and trip notes, reusable templates, YAML frontmatter, an embedded map view, and a
workflow that can sync between desktop and iPhone with Obsidian Sync. The locations are real
and deliberately selected away from the author's home area.

## Status

Proof of concept

## Purpose

A public reference example for Obsidian users who want to build a structured
travel-planning vault. It shows one way to organize places, trips, and templates so that
notes stay consistent and easy to navigate as a vault grows.

## Quick Start

Clone the repository, then open its `Travel/` directory as an Obsidian vault. There is no
build step.

```bash
git clone https://github.com/kevinpinscoe/obsidian-travel-vault-example.git
```

On first open, enable community plugins if Obsidian asks, then confirm that the **Maps**
plugin is enabled in **Settings → Community plugins**. Open `home.md` to see the embedded
map. The core **Templates** plugin is also enabled, pointed at `templates/`.

## Repository Layout

```text
obsidian-travel-vault-example/
├── Travel/                          # Open this directory as the Obsidian vault
│   ├── .obsidian/                    # Portable vault settings, Maps plugin, Templates config
│   ├── home.md                       # Vault landing page and embedded map
│   ├── Places Map.base               # Bases map and coordinates table
│   ├── places/                       # Places, organized by state and city
│   │   ├── FL/Orlando/
│   │   ├── MD/Baltimore/
│   │   ├── NC/Asheville/
│   │   ├── NJ/Atlantic City/
│   │   ├── NJ/Margate City/
│   │   ├── PA/Butler/
│   │   ├── TN/Gatlinburg/
│   │   ├── TN/Pigeon Forge/
│   │   └── VA/Charlottesville/
│   ├── templates/                    # Note templates (place, trip, category indexes, etc.)
│   └── trips/                        # Day-trip and trip-group notes, organized by year
├── .gitignore                        # Excludes local Obsidian and agent state
├── LICENSE                           # CC BY 4.0 for vault content
├── LICENSE-CODE                      # MIT for scripts and plugin code
├── README.md                         # Repository guide
├── THIRD-PARTY-NOTICES.md             # Bundled Maps plugin attribution
└── mise.toml                         # Optional development-tool versions
```

Each sample city demonstrates the `places/<ST>/<City>/` layout and its four category
indexes: places to eat, visit, shop, and stay. `trips/2026/` includes a two-day sample trip
grouped under a `trip-group` note to demonstrate the trip templates end to end.

## Requirements

- [Obsidian 1.13.1 or later](https://obsidian.md/)
- The official [Maps community plugin](https://github.com/obsidianmd/obsidian-maps), included
  in `.obsidian/plugins/maps/` and enabled by the vault configuration
- The core Templates plugin, enabled and pointed at `templates/`. Two templates
  (`new-city.md`, `trip.md`) are Templater scripts included for reference only — the
  Templater community plugin is not installed in this example vault, so they cannot be run
  as-is.
- Obsidian Sync is optional. Configure it with your own account and remote vault; this
  repository contains no account, device, workspace, or Sync credentials.

## Desktop and iPhone Sync

1. Open `Travel/` on the desktop and configure Obsidian Sync for your own remote vault.
2. In Obsidian Sync settings, enable both **Installed community plugin list** and **Active
   community plugin list** so Maps is carried to the iPhone.
3. Open or create the same remote vault in Obsidian on the iPhone and let the initial sync
   finish. Enable community plugins if prompted.
4. On each device, open `home.md`. Its map should display every venue with a complete
   coordinate pair. A venue without coordinates is intentionally absent.

Maps supports mobile Obsidian, but test this short checklist with your own Sync account after
cloning: this public repository cannot include or exercise a personal Sync remote.

## How It Works

The vault organizes travel planning around a small set of note types:

- **Place notes** — one note per location, holding structured facts (address, coordinates,
  notes) in YAML frontmatter so they can be queried and displayed consistently.
- **Trip notes** — one note per travel day, linking to the places visited; a multi-day trip
  is grouped under a trip-group note that holds the shared overview.
- **Templates** — reusable note skeletons in `templates/` for places, category indexes,
  states, trips, and trip groups, so every new note starts with the same frontmatter fields.
- **Map views** — place notes with coordinates in their frontmatter can be rendered on a
  map through Obsidian Bases and the Maps plugin.
- **Cross-device sync (optional)** — the same configuration can travel between desktop and
  iPhone through Obsidian Sync when its plugin-list settings are enabled.

## Security

This vault contains no home-location data, private itineraries, booking references, record
locators, payment details, or account credentials. The place names, coordinates, favorites,
ratings, and selected visit dates are real, but the locations are deliberately chosen away
from the author's home area.

## Ownership and Support

Maintained by Kevin P. Inscoe. Provided as a reference example with no support commitment.

## License

This repository uses a dual license:

- **Vault content** (notes, templates, and sample place data) is licensed under the
  [Creative Commons Attribution 4.0 International License](LICENSE) (CC BY 4.0).
- **Scripts and plugin code** are licensed under the [MIT License](LICENSE-CODE).
- The bundled official Maps plugin remains copyright Obsidian and MIT-licensed; see
  [third-party notices](THIRD-PARTY-NOTICES.md).
