# RUNBOOK.md — Obsidian Travel Vault Example

## Metadata

| Field                 | Value                                                                             |
| --------------------- | --------------------------------------------------------------------------------- |
| **Owner**             | Kevin P. Inscoe                                                                   |
| **Last Updated**      | 2026-09-13                                                                        |
| **Last Tested**       | 2026-09-13 (opened by hand-inspecting note structure; not opened in Obsidian yet) |
| **Expected Duration** | A few minutes to clone and open; a few minutes per new note                       |
| **Risk Level**        | Low — a static example vault with no automation, no credentials, no live service  |
| **Repo**              | <https://github.com/kevinpinscoe/obsidian-travel-vault-example>                   |

---

## Purpose

How to open, extend, and maintain this example vault: enabling the right plugins,
creating each kind of note by hand, and what the frontmatter and `themes` fields
mean. This is a public, privacy-reviewed reference vault, not a live operational
system — there is nothing here to page anyone about.

---

## When to Use This Runbook

- **Use when:** opening this vault for the first time, adding a new place or trip
  note, or troubleshooting a template that inserted the wrong thing.
- **Do NOT use when:** looking for the repository's overall layout or license —
  see [`README.md`](../README.md) instead. This file covers vault operation, not
  repository structure.

---

## Prerequisites

- [ ] [Obsidian](https://obsidian.md/) 1.13.1 or later
- [ ] The bundled official **Maps** community plugin (`Travel/.obsidian/plugins/maps/`) —
      confirm it is enabled under **Settings → Community plugins**
- [ ] The core **Templates** plugin, enabled and pointed at `templates/` — this ships
      already configured in `.obsidian/templates.json`
- [ ] **Not required:** the **Templater** community plugin is *not* installed in this
      vault. Three templates (`new-city.md`, `place.md`, `trip.md`) are written in
      Templater's `<% %>` syntax and are included for reference only — see
      [Templater templates in this example](#templater-templates-in-this-example) below.

---

## Stack

| Component               | Details                                                                    |
| ----------------------- | -------------------------------------------------------------------------- |
| **Application**         | [Obsidian](https://obsidian.md/) — local-first Markdown note app           |
| **Vault location**      | `Travel/` at the repository root (open this directory, not the repo root)  |
| **Version history**     | Git, hosted on GitHub — no Obsidian Sync account is configured or included |
| **Bundled plugins**     | Maps (community), Templates (core)                                         |
| **Credentials/Secrets** | None. This vault stores no account, device, or Sync credentials.           |

---

## Opening the Vault

1. Clone the repository (see the root [`README.md`](../README.md) for the URL).
2. In Obsidian, choose **Open folder as vault** and select the `Travel/` directory —
   not the repository root.
3. If Obsidian prompts about community plugins, allow them, then confirm under
   **Settings → Community plugins** that **Maps** is enabled.
4. Open `home.md` to see the embedded map and the links to `places/` and `trips/`.

Cross-device sync is optional and not included: if you want it, configure Obsidian
Sync with your own account and your own remote vault. This repository contains no
Sync state to carry over — see [Security](#security) below.

---

## Creating Notes

Every kind of note in this vault has a template in `templates/`. Ten of them
(`county.md`, `state-index.md`, `trips-year.md`, `trip-group.md`, and the eight
`places-to-*.md` / `city-places-to-*.md` category templates) use Obsidian's core
`{{ }}` template syntax and can be inserted with **Templates: Insert template**
(`Ctrl+P`, type `insert template` with a space). The other three need Templater —
see the next section.

### Add a venue

A venue is one business, in its own note inside its city's folder —
`places/PA/Butler/Example Diner.md`.

1. Create the note in the city folder, named for the business.
2. Open it in editing mode, run **Templates: Insert template**, and pick the
   template matching the venue:

   | The place is a…          | Template          |
   | ------------------------ | ----------------- |
   | Restaurant, café, diner  | `places-to-eat`   |
   | Museum, park, landmark   | `places-to-visit` |
   | Store, market, outfitter | `places-to-shop`  |
   | Hotel, cabin, campground | `places-to-stay`  |

3. Fill in the frontmatter by hand — `city`, `state`, `county`, `postal_code`,
   `address`, and `latitude`/`longitude` if known. See
   [Frontmatter Reference](#frontmatter-reference) below.
4. Fill in `## Location` with exactly two bullets, address then telephone (omit
   the telephone bullet entirely if there is none — do not leave it blank):

   ```markdown
   ## Location

   * **Address:** 2483 William Flynn Hwy, Butler, PA 16001
   * **Telephone:** +1 724-285-7529
   ```

5. Write `## Summary`, then delete every `## Links` line with no URL — an empty
   `[Yelp]()` is a dead link in reading view.
6. Open the city's matching category note (for example, `Places to shop.md`) and
   add one bullet: `* [[Example Diner]]`.

### Repair or add a category index note

Only needed for a city missing one of its four `Places to …` notes.

1. Create the note in the city folder, named exactly `Places to eat` (or
   `Places to visit`, `Places to shop`, `Places to stay`).
2. Run **Templates: Insert template** and pick the matching **`city-`** template
   (`city-places-to-eat`, and so on).
3. Fill in `city`, `state`, and `county` by hand.

The body is a heading and a list, nothing else:

```markdown
# Places to eat

* [[Biltmore Estate]]
```

### Add a trip day

One note is one day, even a day with no travel.

1. Create the note inside the year folder, named `YYYY-MM-DD Destination, ST` —
   for example, `trips/2026/2026-09-19 Butler, PA.md`.
2. Templater is not installed, so fill in the frontmatter by hand instead of
   running the `trip` template — copy an existing day note (for example,
   [`trips/2026/2026-09-18 Baltimore, MD.md`](trips/2026/2026-09-18%20Baltimore%2C%20MD.md))
   as a starting shape. See [Frontmatter Reference](#frontmatter-reference) for
   the `trip`-specific fields (`trip_date`, `origin`, `destination`, `vacation`).
3. Add stops under `### Morning`, `### Afternoon`, and `### Evening` as links into
   `places/` — `* Dinner — [[Jimmy's Famous Seafood]]`.
4. **Add the day to its year index by hand.** Without Templater there is no
   script to do this automatically — open `trips/<YYYY>/<YYYY>.md` and add a
   line under `## Day trips` (a standalone day) or make sure it is covered by
   its vacation note's entry under `## Vacations` (a grouped day).

### Add a trip-group (vacation) note

Only for a trip longer than one day.

1. Create the note in the year folder, named for the dates or the occasion — it
   must be unique in the vault.
2. Run **Templates: Insert template** and pick `trip-group`.
3. Fill in `start_date` and `end_date`, list the day notes under `## Days`, and
   put anything true of the whole trip under `## Notes`.
4. **The name must match the `vacation:` field on every one of its day notes,
   exactly** — that string becomes a wikilink in the year index, and a near miss
   is a broken link rather than an error.

### Give a place a theme

A theme says what a place *is*, below the level `category` works at — see
[Themes Reference](#themes-reference). It goes on venue notes only.

1. Pick a name from the table below. Do not invent one.
2. Replace the note's `themes: []` with a YAML list — a place can have several:

   ```yaml
   themes:
     - historic_site
   ```

3. Fill in `latitude`/`longitude` if the place has no street address to geocode —
   this example vault includes no geocoding script, so coordinates are always
   entered by hand.

---

## Templater templates in this example

`new-city.md`, `place.md`, and `trip.md` are Templater scripts (`<% %>` syntax),
included so the templates directory shows the vault's full design — but the
**Templater** community plugin is not installed here, and `new-city.md` also
calls two user scripts (`geo_lookup`, `state_name`) that are not included either.
Trying to insert any of the three with the core **Templates** command leaves
literal `<% %>` text in the note instead of expanding it.

Every place note actually in this vault (`places/**/*, ST.md`) was written by
hand, following `place.md`'s structure — its four category links path-qualified
exactly as the template would generate them (`[[places/NC/Asheville/Places to
eat|Places to eat]]`, never a bare `[[Places to eat]]`, which would resolve to
whichever city's copy Obsidian happens to find first).

---

## Frontmatter Reference

Every note carries a YAML frontmatter block. These 36 keys appear on every
template in this vault, in this order:

```yaml
updated: "2026-09-12 18:44"
title: "Example Place"
place_type: venue
category: attraction
status: want_to_visit
visited: false
last_visited:
favorite: false
rating:
latitude:
longitude:
address:
city: Butler
state: PA
county:
country: United States
postal_code:
website:
google_maps_url:
osm_id:
wikidata_id:
priority:
estimated_visit_duration:
best_season:
drive_distance_miles:
drive_time_minutes:
nearest_amtrak_station:
amtrak_routes: []
route_notes:
parking_notes:
accessibility_notes:
pkm_vault: KnowledgeVault
pkm_moc_name:
pkm_note_name:
pkm_private: false
publish: true
```

Fields that appear only on some notes:

| Key                       | Appears on                                 | Value        | Purpose                                                         |
| ------------------------- | ------------------------------------------ | ------------ | --------------------------------------------------------------- |
| `counties`                | city notes (`place.md`)                    | YAML list    | The full span of counties a city straddles; descriptive only    |
| `price_level`             | the eight venue/category templates         | free text    | Rough cost band                                                 |
| `reservation_required`    | same eight                                 | true/false   | Whether you have to book ahead                                  |
| `hours_url`               | same eight                                 | URL          | Opening hours, when they live on their own page                 |
| `themes`                  | same eight                                 | YAML list    | What the place *is* — see [Themes Reference](#themes-reference) |
| `trip_date`               | day note (`place_type: trip`)              | `YYYY-MM-DD` | The day this note covers                                        |
| `origin` / `destination`  | day note                                   | `"City, ST"` | Where the day starts / ends                                     |
| `vacation`                | day note                                   | note name    | The trip-group note this day belongs to; blank if standalone    |
| `start_date` / `end_date` | trip-group note (`place_type: trip_group`) | `YYYY-MM-DD` | First / last day of the trip                                    |

### Valid `place_type` values

| Value            | The note is…                           |
| ---------------- | -------------------------------------- |
| `city`           | a city, town, or place                 |
| `venue`          | one business                           |
| `category_index` | a city's list of venues                |
| `state`          | a state index                          |
| `index`          | a landing note listing other notes     |
| `trip`           | one travel day                         |
| `trip_group`     | a multi-day trip, grouping travel days |

### Valid `category` values

| Value          | Pairs with `place_type`                     |
| -------------- | ------------------------------------------- |
| `place_index`  | `city`                                      |
| `restaurant`   | `venue` or `category_index`                 |
| `attraction`   | `venue` or `category_index`                 |
| `shopping`     | `venue` or `category_index`                 |
| `lodging`      | `venue` or `category_index`                 |
| `state_index`  | `state`                                     |
| `places_index` | `index` — `places/places.md` only           |
| `itinerary`    | `trip` or `trip_group`                      |
| `trips_index`  | `index` — `trips/trips.md` and a year index |

### `status` values (used on both place and trip notes)

| Value           | On a place note       | On a trip note |
| --------------- | --------------------- | -------------- |
| `research`      | Still looking into it | Still planning |
| `want_to_visit` | On the list           | Locked in      |
| `visited`       | Been there            | Taken          |
| `skip`          | Ruled out             | Cancelled      |

`latitude`/`longitude` are bare, unquoted decimal degrees, filled together or not
at all — a half-filled pair is treated as no coordinate at all, since it would
not appear correctly on the map. Never copy a city's centroid onto a venue; a
venue with no coordinate is the honest state.

---

## Themes Reference

A theme says what a place *is*, below the level `category` works at. Names are
lowercase, `snake_case`, and singular. This example vault includes the field on
every venue and category template, though none of its sample venues currently
carry one — add one following the table below if you extend the vault.

| Theme               | The place is…                                                |
| ------------------- | ------------------------------------------------------------ |
| `waterfall`         | A waterfall, of any size                                     |
| `swimming_hole`     | A river or creek pool people swim in                         |
| `lake`              | A lake or reservoir, and its shoreline access                |
| `hot_spring`        | A natural warm or hot spring                                 |
| `state_park`        | A state park                                                 |
| `national_park`     | A unit of the National Park System                           |
| `hiking_trail`      | A trail or trailhead worth going to for its own sake         |
| `summit`            | A mountain top or high point                                 |
| `overlook`          | A pull-off or viewpoint — the view is the destination        |
| `cave`              | A cave or cavern open to visitors                            |
| `gorge`             | A gorge, canyon, or notable rock formation                   |
| `arboretum`         | An arboretum, botanical garden, or public garden             |
| `covered_bridge`    | A covered bridge                                             |
| `fire_tower`        | A fire lookout tower, standing or restored                   |
| `historic_site`     | A place preserved for what happened there                    |
| `museum`            | A museum of any subject                                      |
| `ghost_town`        | An abandoned or near-abandoned settlement                    |
| `heritage_railroad` | A tourist or heritage railway, or a preserved depot          |
| `roadside_oddity`   | A landmark that exists to be looked at                       |
| `barbecue`          | A barbecue joint                                             |
| `diner`             | A classic diner or lunch counter                             |
| `drive_in`          | A drive-in — restaurant or theatre                           |
| `brewery`           | A brewery or taproom                                         |
| `winery`            | A winery, vineyard, or cidery                                |
| `farm_stand`        | A farm stand, orchard, or pick-your-own                      |
| `scenic_drive`      | A road worth driving for the drive                           |
| `dark_sky`          | Somewhere dark enough to be worth going to for the night sky |

Most places have no theme — the empty-list form `themes: []` the templates ship
with is normal, not a gap to fill in.

---

## Verification

```bash
# Confirm the vault opens without a missing-plugin warning:
# Settings -> Community plugins -> Maps should show "Enabled"
```

**Success criteria:** `home.md` renders its embedded map, and every place note
under `places/` opens without a "template not found" or unresolved-link warning.

---

## Troubleshooting

| Symptom                                                               | Likely Cause                                                                | Resolution                                                                                                                                  |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| A note fills with `<% %>` or `tp.file.folder` text instead of content | Ran core **Templates: Insert template** on a Templater-only template        | Delete the inserted text; Templater is not installed in this vault, so `new-city.md`/`place.md`/`trip.md` must be filled in by hand instead |
| A note fills with a literal `{{title}}` heading                       | Tried to run a core-syntax template through a Templater-style command       | There is no Templater command available for these — use **Templates: Insert template** instead                                              |
| A category link (`[[Places to eat]]`) resolves to the wrong city      | The link was left unqualified                                               | Always path-qualify these four links: `[[places/PA/Butler/Places to eat\|Places to eat]]`                                                   |
| A venue does not appear on the map                                    | Missing or half-filled `latitude`/`longitude`                               | Fill in both together, in decimal degrees, or leave both blank                                                                              |
| The wrong template was inserted into an existing note                 | Both insert commands write into whatever note is open, without confirmation | `git checkout -- "<path to note>"` to discard the accidental insert, then start over                                                        |

---

## Security

This vault contains no home-location data, private itineraries, booking
references, record locators, payment details, or account credentials. See the
root [`README.md`](../README.md) → *Security* for the full statement. Keep this
vault that way: anything sensitive belongs in a private note store behind a
pointer field (`pkm_vault`, `pkm_moc_name`, `pkm_note_name`), never inline here —
this repository is public.

---

## Related Documentation

- [`README.md`](../README.md) — what this repository is, its layout, and its license
- [`THIRD-PARTY-NOTICES.md`](../THIRD-PARTY-NOTICES.md) — bundled Maps plugin attribution

---

## Maintenance Notes

- **Last game-day test:** none — this is a static example vault with nothing to
  fail unattended.
- **Known drift risks:** the Repository Layout in `README.md` and the frontmatter
  reference above should be re-checked whenever a template or a new state/city
  is added to `places/`.
