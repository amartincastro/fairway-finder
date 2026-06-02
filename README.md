# Fairway Finder

A map-based golf course finder for the continental US. Built as an MVP to test the product idea — a single-page app with filters, plotted from OpenStreetMap data.

## Live

Hosted on GitHub Pages. Open `index.html` locally or deploy this folder to a `gh-pages` branch (or set Pages source to `main` / root).

## What it does

- Plots ~14,200 US golf courses on a Leaflet map with marker clustering
- Sidebar lists courses currently in the map viewport (updates as you pan/zoom)
- Filters: name/city search, hole count (9 / 18 / 27+), require website, require phone
- "Near me" uses geolocation to fly to your area
- Click a pin or list row for name, address, phone, website

## Data

Sourced from OpenStreetMap (`leisure=golf_course`) via the Overpass API. Licensed under the [Open Database License (ODbL)](https://www.openstreetmap.org/copyright) — attribution to OpenStreetMap contributors is in the map footer.

Field coverage in the bundled dataset:

| Field    | Coverage |
| -------- | -------- |
| name     | 95%      |
| website  | 50%      |
| phone    | 42%      |
| address  | ~47%     |
| holes    | 10%      |
| par      | 8%       |

The interesting filters for an actual product (public/private, green fees, ratings, amenities) are **not** in OSM and would need a paid API or scraping/enrichment.

## Refreshing the dataset

To regenerate `courses.js` from a fresh Overpass query:

```bash
curl -A "FairwayFinder/0.1" -X POST "https://overpass-api.de/api/interpreter" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode 'data=[out:json][timeout:540];(way["leisure"="golf_course"](24.0,-125.0,50.0,-66.0);relation["leisure"="golf_course"](24.0,-125.0,50.0,-66.0);node["leisure"="golf_course"](24.0,-125.0,50.0,-66.0););out center tags;' \
  -o us-raw.json
```

Then slim the result and wrap it as `window.GOLF_COURSES = [...]` in `courses.js`.

## Stack

- Leaflet 1.9.4 + leaflet.markercluster 1.5.3 via unpkg
- OpenStreetMap raster tiles
- No build step. No framework. Just `index.html` + `courses.js`.
