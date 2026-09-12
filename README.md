# obsidian-travel-vault-example

## Summary

A privacy-safe example of an Obsidian travel-planning vault. It demonstrates structured
place and trip notes, reusable templates, YAML frontmatter, map views, and a workflow that
syncs smoothly between desktop and iPhone with Obsidian Sync. The locations are real but
intentionally unrelated to the author's personal travel history.

## Status

Proof of concept

## Purpose

A public reference example for Obsidian users who want to build a structured
travel-planning vault. It shows one way to organize places, trips, and templates so that
notes stay consistent and easy to navigate as a vault grows.

## Quick Start

Clone the repository and open the vault folder directly in Obsidian — no build step or
installation is required.

```bash
git clone https://github.com/kevinpinscoe/obsidian-travel-vault-example.git
```

> **Note:** This repository is not yet populated with the redacted example content. The
> vault folder, its notes, and its templates will be added once a privacy-reviewed copy of
> the source vault is ready.

## Requirements

- [Obsidian](https://obsidian.md/)
- Required community plugins will be documented here once the redacted vault content is in
  place.
- Obsidian Sync is optional and not required to use this vault — configure it with your own
  account if you want cross-device sync.

## How It Works

The vault organizes travel planning around a small set of note types:

- **Place notes** — one note per location, holding structured facts (address, coordinates,
  notes) in YAML frontmatter so they can be queried and displayed consistently.
- **Trip notes** — link together the places visited on a given trip, giving each trip its
  own itinerary and narrative.
- **Templates** — reusable note skeletons for places and trips, so every new note starts
  with the same structure and frontmatter fields.
- **Map views** — place notes with coordinates in their frontmatter can be rendered on a
  map, giving a visual view of where a trip goes.
- **Cross-device sync (optional)** — the same vault structure works whether edited on
  desktop or mobile; Obsidian Sync keeps both in sync when configured.

## Security

This vault contains no personal travel history or sensitive personal-location data. The
place names and coordinates included are real, but deliberately unrelated to the author's
actual travel history, and no booking references, record locators, payment details, or
other personal information appear anywhere in the vault.

## Ownership and Support

Maintained by Kevin P. Inscoe. Provided as a reference example with no support commitment.

## License

This repository uses a dual license:

- **Vault content** (notes, templates, and sample place data) is licensed under the
  [Creative Commons Attribution 4.0 International License](LICENSE) (CC BY 4.0).
- **Scripts and plugin code** are licensed under the [MIT License](LICENSE-CODE).
