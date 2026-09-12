<%*
/* -----------------------------------------------------------------------------
 * trip — one leg of a trip: a single day, from an origin to a destination.
 *
 * Prompts for the origin, the destination, the date, and the vacation note this
 * day belongs to, then fills in the frontmatter, the heading, and the
 * "MM-DD (Day)" day heading.
 *
 * It also adds this trip to its year index at trips/<YYYY>/<YYYY>.md, so that
 * index cannot drift — the same job new-city.md does when it appends a new
 * state to places/places.md. A trip with a vacation is listed by its vacation
 * note under "## Vacations"; one without is listed by its own note under
 * "## Day trips". Nothing is ever added twice, and nothing is overwritten.
 *
 * This is a Templater template. Insert it with the command
 * "Templater: Open insert template modal" — that is its real name in the
 * palette; there is no "Templater: Insert Template". The core Templates command
 * copies this file in verbatim instead of running it.
 *
 * Why Templater rather than core Templates: the note needs the trip's date in
 * two forms — YYYY-MM-DD in the frontmatter and MM-DD (dddd) in the day heading
 * — and core Templates can only produce today's date, never a date you name.
 *
 * Create the note first, named "YYYY-MM-DD Destination, ST", then insert this.
 * Inserting appends at the cursor, so run it on a new, empty note — inserting
 * into a note that already has content leaves two of everything.
 * -------------------------------------------------------------------------- */

const DATE_FMT = "YYYY-MM-DD";

const origin = await tp.system.prompt("Origin — City, ST");
if (!origin) return;

const destination = await tp.system.prompt("Destination — City, ST", origin);
if (!destination) return;

const tripDate = await tp.system.prompt("Trip date — YYYY-MM-DD", tp.date.now(DATE_FMT));
if (!tripDate) return;

if (!/^\d{4}-\d{2}-\d{2}$/.test(tripDate.trim())) {
    new tp.obsidian.Notice(`"${tripDate}" is not a YYYY-MM-DD date.`, 10000);
    return;
}

/* Blank is a real answer here: a standalone day trip belongs to no vacation.
 *
 * Whatever is typed is written into the year index as a wikilink, so it has to
 * match the vacation note's name character for character — "Anniversary 2026",
 * not "anniversary 2026" or "Anniversary 2026 trip". A near miss is not an
 * error; it is a broken link in the index and a day note filed under a trip
 * that does not exist. */
const vacation = (await tp.system.prompt(
    "Vacation note this day belongs to — exact name, blank if none",
)) || "";

const date = tripDate.trim();
const from = origin.trim();
const to = destination.trim();
const title = `${from} → ${to}`;

/* tp.date.now formats a date you hand it when given a reference and its format,
 * which is the whole reason this template is Templater rather than core. */
const dayHeading = tp.date.now("MM-DD (dddd)", 0, date, DATE_FMT);

/* --- add this trip to its year index --------------------------------------
 *
 * Everything below only touches trips/<YYYY>/<YYYY>.md. It never modifies the
 * note being written, and it gives up quietly-but-audibly rather than guessing.
 */

/* Insert an entry at the end of a heading's bullet list.
 *
 * The list ends at the first non-blank, non-bullet line, which is what keeps
 * the "_Back to [[trips]]._" footer below "## Day trips" from being treated as
 * part of the list and pushed around. Returns null if the heading is absent. */
function addUnderHeading(text, heading, entry) {
    const lines = text.split("\n");
    const start = lines.findIndex((l) => l.trim() === heading);
    if (start === -1) return null;

    let last = start;
    for (let i = start + 1; i < lines.length; i++) {
        if (/^\s*$/.test(lines[i])) continue;
        if (/^\s*[-*] /.test(lines[i])) { last = i; continue; }
        break;
    }

    let at = last === start ? start + 1 : last + 1;
    /* With no bullets yet, keep the blank line that follows the heading. */
    if (last === start && /^\s*$/.test(lines[at] || "")) at++;

    lines.splice(at, 0, entry);

    /* Keep a blank line between the list and whatever follows it. Without one,
     * Markdown treats the next paragraph as a lazy continuation of the last
     * bullet, so the "_Back to [[trips]]._" footer renders inside it. */
    const next = lines[at + 1];
    if (next !== undefined && !/^\s*$/.test(next) && !/^\s*[-*] /.test(next)) {
        lines.splice(at + 1, 0, "");
    }

    return lines.join("\n");
}

const year = date.slice(0, 4);
const yearFolder = `trips/${year}`;
const indexPath = `${yearFolder}/${year}.md`;

/* Only touch the index when this note genuinely lives in that year's folder.
 * Without this guard, inserting the template into a scratch note anywhere in
 * the vault would silently add a line to a real year index. */
const noteFolder = tp.file.folder(true);
const indexFile = app.vault.getAbstractFileByPath(indexPath);

if (noteFolder !== yearFolder) {
    new tp.obsidian.Notice(
        `Year index not updated: this note is in "${noteFolder}", not "${yearFolder}". ` +
        `Move it there and re-run, or add the link by hand.`,
        10000,
    );
} else if (!indexFile) {
    new tp.obsidian.Notice(
        `Year index not updated: "${indexPath}" does not exist. ` +
        `Create it from templates/trips-year.md, then add the link by hand.`,
        10000,
    );
} else {
    /* A trip that belongs to a vacation is indexed by its vacation note — one
     * line per trip, not one per day. A standalone day is indexed by itself. */
    const linkTo = vacation.trim() || tp.file.title;
    const heading = vacation.trim() ? "## Vacations" : "## Day trips";
    const entry = `- [[${linkTo}]]`;

    const before = await app.vault.read(indexFile);

    if (before.includes(`[[${linkTo}]]`)) {
        new tp.obsidian.Notice(`Already in ${year}.md: ${linkTo}`, 5000);
    } else {
        const after = addUnderHeading(before, heading, entry);
        if (after === null) {
            new tp.obsidian.Notice(
                `Year index not updated: "${indexPath}" has no "${heading}" heading.`,
                10000,
            );
        } else {
            await app.vault.modify(indexFile, after);
            new tp.obsidian.Notice(`Added to ${year}.md under ${heading}: ${linkTo}`, 5000);
        }
    }
}
-%>
---
updated: "<% tp.date.now('YYYY-MM-DD HH:mm') %>"
title: "<% title %>"
place_type: trip
category: itinerary
trip_date: <% date %>
origin: "<% from %>"
destination: "<% to %>"
vacation: <% vacation %>
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
pkm_private: true
publish: true
---
# <% title %>

## <% dayHeading %>

### Morning

### Afternoon

### Evening

## Notes

<!-- Stops go in the time-of-day sections above, as links into places/ —
     "* Lunch — [[Mill Street Grill]]". Venue names are distinctive enough to
     link bare; path-qualify only a name that repeats across cities.

     No booking details in this vault: no confirmation numbers, no record
     locators, no payment information. Those live in ~/KnowledgeVault — put a
     pkm_note_name pointer in the frontmatter instead. -->
