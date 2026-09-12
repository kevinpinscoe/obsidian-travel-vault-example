<%*
/* -----------------------------------------------------------------------------
 * new-city — scaffold a city folder under places/<ST>/
 *
 * Prompts for a city name, resolves it against the local GeoNames index to get
 * its state, county, and coordinates, then creates:
 *
 *   places/<ST>/                     (folder, if the state is new)
 *   places/<ST>/<ST>.md              (state index note, if the state is new)
 *   places/<ST>/<City>/              (folder)
 *   places/<ST>/<City>/<City>, <ST>.md
 *   places/<ST>/<City>/Places to eat.md
 *   places/<ST>/<City>/Places to visit.md
 *   places/<ST>/<City>/Places to shop.md
 *   places/<ST>/<City>/Places to stay.md
 *
 * and links a newly created state from places/places.md.
 *
 * Nothing is ever overwritten — an already-present note is skipped and counted
 * in the summary notice.
 *
 * This template writes no text into the note it is run from. Run it with
 * Templater's "Insert Template" command or a hotkey bound through Templater's
 * Template Hotkeys setting; either way, a note must be open for Templater to
 * run against.
 *
 * Requires Templater's "User Scripts folder" set to `scripts/templater`.
 * -------------------------------------------------------------------------- */

const { Notice } = tp.obsidian;

const PLACE_TEMPLATE = "templates/place.md";
const STATE_TEMPLATE = "templates/state-index.md";
const CATEGORY_TEMPLATES = [
    ["Places to eat", "templates/city-places-to-eat.md"],
    ["Places to visit", "templates/city-places-to-visit.md"],
    ["Places to shop", "templates/city-places-to-shop.md"],
    ["Places to stay", "templates/city-places-to-stay.md"],
];

/* --- helpers -------------------------------------------------------------- */

function fail(message) {
    new Notice(message, 10000);
}

async function templateText(path) {
    const file = app.vault.getAbstractFileByPath(path);
    if (!file) throw new Error(`Template not found: ${path}`);
    return await app.vault.read(file);
}

/* Expand core-Templates tokens ourselves. The category and state templates stay
 * in {{ }} syntax so they remain insertable by hand with the core Templates
 * command; Templater would leave those tokens untouched, so this command fills
 * them in before handing the content over. */
function renderCore(text, title) {
    return text
        .replace(/\{\{title\}\}/g, title)
        .replace(/\{\{date:([^}]+)\}\}/g, (_, fmt) => tp.date.now(fmt))
        .replace(/\{\{time:([^}]+)\}\}/g, (_, fmt) => tp.date.now(fmt));
}

/* Replace one top-level frontmatter value, leaving key order untouched. Anchored
 * to the start of a line, so body text like "**Address:**" is never matched. */
function setField(text, key, value) {
    const re = new RegExp(`^${key}:.*$`, "m");
    return re.test(text) ? text.replace(re, `${key}: ${value}`) : text;
}

async function ensureFolder(path) {
    let folder = app.vault.getAbstractFileByPath(path);
    if (!folder) {
        await app.vault.createFolder(path);
        folder = app.vault.getAbstractFileByPath(path);
    }
    return folder;
}

/* --- 1. resolve the city -------------------------------------------------- */

const query = await tp.system.prompt("City or town name — optionally \"City, ST\"");
if (!query) return;

let matches;
try {
    matches = await tp.user.geo_lookup(query);
} catch (error) {
    fail(error.message);
    return;
}

let hit;
if (matches.length === 0) {
    /* GeoNames only lists populated places, so a resort, a hamlet, or a spot
     * known by a local name will not be there. Fall back to entering the state
     * by hand rather than dead-ending; county and coordinates stay empty for
     * scripts/add-county.sh and scripts/add-coordinates.sh to fill later. */
    const manual = await tp.system.prompt(
        `"${query}" is not in the GeoNames index. State abbreviation to file it under (blank to cancel)`,
    );
    if (!manual) return;
    hit = {
        name: query.trim(),
        state: manual.trim().toUpperCase(),
        county: "",
        latitude: "",
        longitude: "",
    };
} else if (matches.length === 1) {
    hit = matches[0];
} else {
    const labels = matches.map(
        (m) => `${m.name}, ${m.state} — ${m.county || "county unknown"} (pop. ${m.population.toLocaleString()})`,
    );
    hit = await tp.system.suggester(labels, matches, false, `${matches.length} matches — which one?`);
    if (!hit) return;
}

if (!/^[A-Z]{2}$/.test(hit.state)) {
    fail(`"${hit.state}" is not a two-letter state abbreviation.`);
    return;
}

/* Confirm the folder name. GeoNames writes names out in full, but the vault
 * abbreviates some of them (places/TN/St. James, not Saint James), so the
 * resolved name is offered as an editable default. */
const cityInput = await tp.system.prompt("Folder name for this place", hit.name);
if (!cityInput) return;

const city = cityInput.trim();
const st = hit.state;

if (/[\\/:]/.test(city)) {
    fail(`"${city}" contains a character that cannot be used in a folder name.`);
    return;
}

const cityTitle = `${city}, ${st}`;
const stateFolderPath = `places/${st}`;
const cityFolderPath = `${stateFolderPath}/${city}`;

const created = [];
const skipped = [];

/* --- 2. the state ---------------------------------------------------------- */

const stateFolder = await ensureFolder(stateFolderPath);
const stateNotePath = `${stateFolderPath}/${st}.md`;

if (!app.vault.getAbstractFileByPath(stateNotePath)) {
    const stateTitle = `${tp.user.state_name(st)} (${st})`;
    let body = renderCore(await templateText(STATE_TEMPLATE), stateTitle);
    body = setField(body, "state", st);
    await tp.file.create_new(body, st, false, stateFolder);
    created.push(`${st}.md`);
}

/* Link the state from places/places.md whether or not this run created it —
 * a state folder can predate its entry in the index. */
const placesIndex = app.vault.getAbstractFileByPath("places/places.md");
if (placesIndex) {
    const text = await app.vault.read(placesIndex);
    if (!text.includes(`[[${st}]]`)) {
        await app.vault.modify(placesIndex, `${text.trimEnd()}\n- [[${st}]] — ${tp.user.state_name(st)}\n`);
        created.push("places.md link");
    }
}

/* --- 3. the city note ------------------------------------------------------ */

const cityFolder = await ensureFolder(cityFolderPath);
const cityNotePath = `${cityFolderPath}/${cityTitle}.md`;

if (app.vault.getAbstractFileByPath(cityNotePath)) {
    skipped.push(`${cityTitle}.md`);
} else {
    /* place.md is a Templater template — pass the TFile so Templater expands its
     * tp.file.folder(true) expression against the new file's own folder, then
     * fill in the fields the template cannot know.
     *
     * Never write a Templater closing tag inside this block, not even in a
     * comment: the parser scans for it textually and would end the block here. */
    const placeTemplate = app.vault.getAbstractFileByPath(PLACE_TEMPLATE);
    if (!placeTemplate) {
        fail(`Template not found: ${PLACE_TEMPLATE}`);
        return;
    }
    const file = await tp.file.create_new(placeTemplate, cityTitle, false, cityFolder);

    let text = await app.vault.read(file);
    text = setField(text, "title", `"${cityTitle}"`);
    text = setField(text, "city", city);
    text = setField(text, "state", st);
    text = setField(text, "county", hit.county);
    text = setField(text, "latitude", hit.latitude);
    text = setField(text, "longitude", hit.longitude);
    text = text.replace(/^# Place, ST$/m, `# ${cityTitle}`);
    await app.vault.modify(file, text);

    if (text.includes("<%")) {
        fail(`${cityTitle}.md still contains unexpanded Templater syntax — check it by hand.`);
    }
    created.push(`${cityTitle}.md`);
}

/* --- 4. the four category index notes -------------------------------------- */

for (const [name, templatePath] of CATEGORY_TEMPLATES) {
    if (app.vault.getAbstractFileByPath(`${cityFolderPath}/${name}.md`)) {
        skipped.push(`${name}.md`);
        continue;
    }
    let body = renderCore(await templateText(templatePath), name);
    body = setField(body, "city", city);
    body = setField(body, "state", st);
    body = setField(body, "county", hit.county);
    await tp.file.create_new(body, name, false, cityFolder);
    created.push(`${name}.md`);
}

/* --- 5. report and open ----------------------------------------------------- */

const summary =
    `${cityTitle}: created ${created.length}` +
    (skipped.length ? `, skipped ${skipped.length} already present` : "");
new Notice(summary, 8000);

await app.workspace.openLinkText(cityNotePath, "", false);
-%>
