# BUILD416

An interactive map of the tech companies clustered in Toronto rendered as a terminal-style operations console. Click a company to fly to its office, click its marker for funding details, and submit new companies to BUILD416 through the built-in form.

Live at **[build416.ca](https://build416.ca)**.

![status: live](https://img.shields.io/badge/status-live-056540) ![PRs welcome](https://img.shields.io/badge/PRs-welcome-056540)

---

## What It Is

A single self-contained web app with no build step, no framework, no backend. It renders an open-source vector map (MapLibre GL) and overlays a community-maintained dataset of Toronto tech companies. The whole thing is three data files plus one HTML file, served as static assets.

**Stack:**
- **[MapLibre GL JS](https://maplibre.org/)** — open-source WebGL map renderer (the open fork of Mapbox GL).
- **[OpenFreeMap](https://openfreemap.org/)** — free vector tile host and base styles. No API key, no usage limits.
- Vector tiles follow the **[OpenMapTiles schema](https://openmaptiles.org/schema/)** (source-layers: `building`, `water`, `transportation`, `place`, `poi`, etc.).
- **[Geist Mono](https://vercel.com/font)** for all UI typography.
- **[FormSubmit](https://formsubmit.co/)** for the "add a company" form (static-site email relay, no server).

**Design:** monochrome near-black console with Solarium green (`#056540`) as the only accent. Top-down 2D blueprint view by default, with a `[3D]` toggle. Street labels appear only on major roads; default map POIs are hidden so only company markers show.

---

## Repository Layout

```
build416/
├── index.html              # The entire app: map, sidebar, popovers, form, loader
├── companies.json          # Company dataset — the heart of the project
├── ticker.json             # Scrolling headline ticker (funding news, milestones)
├── README.md               # You are here
├── LICENSE                 # MIT — covers the code
├── LICENSE-DATA            # CC BY 4.0 — covers the datasets
├── CODEOWNERS              # Routes every PR to the maintainer for review
└── .github/
    └── PULL_REQUEST_TEMPLATE.md
```

---

## Data Format

### `companies.json`

An array of company objects. This is the file 95% of contributions will touch.

```json
{
  "name": "Float",
  "domain": "floatfinancial.com",
  "address": "1 University Ave, Toronto, ON",
  "lat": 43.64712640221162,
  "lng": -79.39503236322123,
  "funding": { "type": "Series B", "amount": "$70M", "date": "2025-04" },
  "tag": "Business finance",
  "careers": "https://floatfinancial.com/careers"
}
```

| Field | Required | Notes |
|---|---|---|
| `name` | ✅ | Display name. |
| `domain` | ✅ | Bare domain (no `https://`, no `www`). Used to fetch the logo and link the website. |
| `lat`, `lng` | ✅ | Decimal degrees. Must fall inside the Toronto core (see validation below). |
| `funding` | ✅ | Object with `type` (e.g. `Seed`, `Series A`, `Public`, `Bootstrapped`, `Acquired`). `amount` and `date` optional. |
| `address` | ⬜ | Human-readable, for provenance. |
| `tag` | ⬜ | Short descriptor shown when there's no funding amount. |
| `careers` | ⬜ | Hiring page URL. Renders a `[HIRING]` chip in the popover. |

**Coordinate Validation:** every entry must sit within the bounding box `[-79.46, 43.62]` → `[-79.34, 43.68]`. Entries outside it are skipped at load with a console warning. Use [latlong.net](https://www.latlong.net/) or right-click → "What's here?" in Google Maps to get coordinates from an address.

### `ticker.json`

An array of headline objects scrolling across the top:

```json
{ "label": "RAISE", "text": "Wealthsimple raises $750M at a $10B valuation" }
```

Keep `label` short (one or two words, uppercased in the UI) and `text` to a single sentence.

---

## Running It Locally

The app fetches `companies.json` and `ticker.json` at runtime, so it **must be served over HTTP** — opening `index.html` directly with `file://` will fail on CORS. Any static server works:

```bash
# Python (no install needed on most machines)
python3 -m http.server 8000

# or Node
npx serve .
```

Then open **http://localhost:8000**. THAT'S IT!

---

## Setting This Up With Claude Code

> This section is written so you can point your local Claude Code at the repo and have it scaffold, run, and verify everything. Paste the block below to it.

**Prompt for Claude Code:**

```
This is BUILD416, a static MapLibre GL JS map of Toronto tech companies.
There is no build step. Please:

1. Read README.md and the data files (index.html, companies.json,
   ticker.json) to understand the structure before changing anything.
2. Start a local static server (`python3 -m http.server 8000`) and open
   http://localhost:8000 to confirm the map renders and the loader dismisses.
3. Verify every company in companies.json:
   - coordinates fall inside the bbox [-79.46,43.62]→[-79.34,43.68]
   - `domain` is a bare domain that resolves to a real favicon
   - no duplicate entries (same name appearing twice)
4. Flag any company whose lat/lng is shared with another, and confirm the
   marker fan-out behaviour handles stacked pins (1 University Ave currently
   has 7 tenants).
5. Confirm markers stay pixel-locked when panning — the element passed to
   `new maplibregl.Marker({element})` must carry NO css transform, transition,
   or animation on its root; all styling lives on an inner child.
6. Do not commit or push. Show me a diff and a summary first.
```

**House Rules For Claude Code On This Repo** (add these to your `CLAUDE.md` if you keep one):

- Never commit or push without explicit approval — always show a diff first.
- `companies.json` is the single source of truth; the sidebar, markers, and counts all derive from it. Don't hardcode company data in `index.html`.
- The `STAT` line in `ticker.json` and any "N companies" count in the UI should read from `companies.length`, never a hardcoded number.
- Keep it dependency-free and single-file. No bundler, no framework.
- Solarium green is `#056540`; it's the only accent color. Don't introduce new hues.

---

## Contributing

Contributions are welcome — especially adding companies, fixing coordinates, and updating funding rounds. **Anyone can open a pull request; all PRs are reviewed and merged by the maintainer.**

### Adding Or Editing A Company

1. **Fork** the repo and create a branch (`add-acme-corp`).
2. Edit `companies.json`. Keep the array readable — one company per block, alphabetical-ish is nice but not required.
3. Make sure your JSON is valid:
   ```bash
   python3 -m json.tool companies.json > /dev/null && echo OK
   ```
4. Run it locally and confirm your pin lands in the right spot and the logo loads.
5. Open a PR. Fill out the template. The maintainer reviews and merges.

### What Gets Accepted

- ✅ Real companies with a genuine office/presence in the mapped area.
- ✅ Accurate, sourceable funding info.
- ✅ Coordinate and funding corrections to existing entries.
- ❌ Companies outside the Toronto core bounding box.
- ❌ Unverifiable funding claims or marketing copy.
- ❌ Duplicate entries.

---

## Credits & Data Sources

Map data © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors, served via OpenFreeMap. Company data is community-contributed and compiled from public announcements. Built by [Nelson Lee](https://github.com/NelsonLee-Code) for the Toronto tech community.

## License

The **code** is [MIT licensed](./LICENSE). The **datasets** (`companies.json`, `ticker.json`) are licensed [CC BY 4.0](./LICENSE-DATA) — standard software licenses don't fit factual data well, so the two are licensed separately. Map base data is © OpenStreetMap contributors under the [ODbL](https://www.openstreetmap.org/copyright); keep the attribution control visible on the map.
