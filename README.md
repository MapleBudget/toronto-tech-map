# Toronto Tech

An interactive 3D map of tech companies and startups around King West / Spadina, Toronto — styled as a dark terminal / ops-console.

## Stack

- **MapLibre GL JS** (v4.7.1) — WebGL vector map renderer
- **OpenFreeMap** — free vector tiles (`liberty` style, OpenMapTiles schema, no API key)
- Single self-contained `index.html` (inline CSS/JS), data in JSON files

## Run locally

The page fetches `companies.json` / `ticker.json`, so it must be served over HTTP — opening `index.html` via `file://` will not work (browser CORS).

```bash
python3 -m http.server 8765
# then open http://127.0.0.1:8765/index.html
```

## Editing content

- **`companies.json`** — single source of truth for the map. Each entry:
  ```json
  {
    "name": "Float",
    "domain": "floatfinancial.com",
    "address": "King St W, Toronto",
    "logo": "assets/logos/float.png",
    "lat": 43.6471, "lng": -79.3950,
    "funding": { "type": "Series B", "amount": "$70M" },
    "tag": "Optional descriptor"
  }
  ```
  - `logo` is optional — drop a file in `assets/logos/` and reference it; otherwise the favicon service is used with an initial-letter fallback.
  - To capture accurate coordinates, right-click the **ground-level entrance** in Google Maps (top-down, not 3D/satellite tilt) → "What's here?" — avoids rooftop parallax on tall buildings.
- **`ticker.json`** — the scrolling headline ticker (`{ "label": "RAISE", "text": "…" }`).

## Features

- 2D blueprint footprints by default; `[3D]` toggle for grey building extrusions
- Company logo markers (co-located pins fan out), click for funding popover
- Sidebar list, live status bar, scrolling ticker
- "Add a company" form → FormSubmit relay
- Mobile bottom-sheet layout
