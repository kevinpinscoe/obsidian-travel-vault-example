---
updated: "2026-09-12 18:44"
title: "Trips"
place_type: index
category: trips_index
status: research
visited: false
last_visited:
favorite: false
rating:
latitude:
longitude:
address:
city:
state:
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
---
# Trips

Road trips, organized by year. Each year has its own folder and index note.

## How a trip is filed

One note per **day**. A day runs from where you start to where you end up, and
its note is named for the date and the destination:

```text
trips/2026/2026-09-18 Baltimore, MD.md
```

The date prefix does real work — it sorts the folder chronologically with no
configuration, and it keeps a repeat destination from colliding with an earlier
visit. A day with no travel, a rest day in the middle of a stay, still gets its
own note, with `origin` and `destination` set to the same city.

A trip spanning several days also gets a **vacation note** grouping its days —
where the route, the overview, and anything true of the whole trip lives. A
single-day trip needs no vacation note.

New day notes are made with the `trip` template, new vacation notes with
`trip-group`. `trip` is a **Templater** template, so it needs Templater's
*Insert Template* command rather than the core Templates one — not installed in
this example vault, so its day notes here were filled in by hand instead.

## Years

- [[2026]] — trips planned or taken in 2026
