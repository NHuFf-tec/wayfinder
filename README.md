# Wayfinder

A road trip planner that turns "Austin to Moab" into a day-by-day itinerary with national parks, campgrounds, scenic stops, and Reddit traveler tips. No backend, no install, no API keys required to start.

![Wayfinder screenshot placeholder — replace with your own](screenshots/preview.png)

## ⚠️ Important: don't double-click `index.html`

The app needs to be served over `http://` or `https://` — not `file://`. Most browsers block `fetch()` calls (routing, park data, Reddit, etc.) from local file pages because the origin is `null`. The map will load but **"Chart the Route" will fail**.

**Easiest fix — run a quick local server:**

```bash
cd wayfinder
python3 -m http.server 8000
# then open http://localhost:8000
```

(Python 3 is built into Mac and Linux. On Windows: `py -m http.server 8000`, or install Python free from python.org.)

**Other options:** `npx serve`, VS Code "Live Server" extension, or push to GitHub Pages / Netlify (where it'll Just Work, since they serve over HTTPS).

## What it does

- **Multi-stop routing** with autocomplete for any city, town, or address in North America
- **Real driving times** from OSRM (OpenStreetMap routing engine)
- **Day-by-day itinerary** with configurable hours-per-day driving (3 hrs leisurely → 9 hrs marathon)
- **National parks** within 50 miles of your route, with descriptions, activities, and direct links to NPS.gov
- **Federal campgrounds** from Recreation.gov — reservable sites across BLM, Forest Service, NPS, and Army Corps land
- **Reddit traveler tips** for each stop, pulled from r/roadtrip, r/NationalParks, r/CampingandHiking, r/travel, and r/StateParks
- **State parks** — direct links to the official site for every state your route crosses
- **TripAdvisor + Reddit deep-search** buttons on every recommended stop, one click away
- **Interactive map** with route polyline, lettered start/end markers, and clickable POI pins

## Quick start

```bash
git clone https://github.com/yourname/wayfinder.git
cd wayfinder
python3 -m http.server 8000
# open http://localhost:8000
```

Or push to GitHub Pages and the live URL Just Works — there's no build step.

## Optional API keys

The app works without any keys, but adding two free keys unlocks much richer data. Click the **API Keys** button in the header.

| Service | What it adds | Get a key |
|---|---|---|
| **National Park Service** | Authoritative park descriptions, activities, hours, fees | [nps.gov/subjects/developer](https://www.nps.gov/subjects/developer/get-started.htm) |
| **Recreation.gov (RIDB)** | Federal campgrounds, reservable sites, recreation areas | [ridb.recreation.gov/profile](https://ridb.recreation.gov/profile) |

Both keys are free, take under a minute to get, and are stored only in your browser's localStorage. They are never sent anywhere except the official endpoints.

## Data sources

Wayfinder is built entirely on free, public APIs — no paid services, no backend.

| Source | Used for | Key required |
|---|---|---|
| [Nominatim](https://nominatim.openstreetmap.org/) | Geocoding (city/address lookup) | No |
| [OSRM](http://project-osrm.org/) | Driving routes & times | No |
| [Overpass API](https://overpass-api.de/) | Restaurants, hotels, scenic viewpoints, historic sites | No |
| [CARTO Basemaps](https://carto.com/) | Map tiles | No |
| [Reddit JSON API](https://www.reddit.com/dev/api) | Traveler discussions | No |
| [NPS Data API](https://www.nps.gov/subjects/developer/) | National park details | Free |
| [Recreation.gov RIDB](https://ridb.recreation.gov/) | Federal campgrounds | Free |

## Why no TripAdvisor API?

The TripAdvisor Content API requires server-side proxying (no CORS), an approved live website with monthly traffic, billing setup, and use of their attribution images. None of that fits a static-HTML, no-backend tool. Instead, every recommended stop has a one-click button that opens a pre-filled TripAdvisor search — same outcome, zero friction.

## Browser support

Tested on current Chrome, Firefox, Safari, and Edge. Uses ES2020 features (optional chaining, async/await, etc.) so very old browsers won't work.

## Limits

The free public APIs Wayfinder uses have concrete limits. Here's everything that matters for a real trip:

| Constraint | Limit | What it affects |
|---|---|---|
| **OpenRouteService — single request** | 6,000 km (~3,728 mi) | Wayfinder auto-chunks longer routes into multiple requests, transparently. |
| **OpenRouteService — daily** | 2,000 directions calls/day | Plenty for personal use. A typical trip plan uses 1-3 calls. |
| **OpenRouteService — per minute** | 40 calls/min | Plenty even when chunking long routes. |
| **Nominatim geocoder** | ~1 req/sec, fair use | Wayfinder rate-limits internally to 1.1 sec between calls and caches repeats. |
| **Foursquare Places (free tier)** | 10,000 calls/month + $200/mo credit | A trip plan uses ~6-12 calls. |
| **NPS API** | No published limit, polite use expected | Each plan uses 1 call total. |
| **Recreation.gov** | No published limit | ~2-5 calls per plan. |
| **Reddit** | Subject to browser tracking prevention | Often blocked in Edge/Brave; works in Chrome/Firefox. Deep-link buttons always work. |

### Trip length ceilings

- **Single-direction routes**: any distance, including coast-to-coast, single ORS request.
- **Round Trip with simple loop**: routes up to ~1,800 mi each way (so total ~3,600 mi loop) fit one ORS request; longer routes auto-chunk.
- **Grand Loop mode**: generates a multi-region perimeter trip with 3-6 anchor cities. A continental US perimeter loop is roughly 8,000-12,000 miles; this is auto-chunked into 3-4 ORS requests. Plan for 20-30 driving days at 5 hrs/day.
- **Day count**: capped at 30 days in the UI, but the splitter will generate as many days as needed for the route under your hours-per-day budget.

### What can still go wrong

- **OSRM demo servers down**: if you don't have an ORS key, routing falls back to public OSRM mirrors, which routinely 502. Adding a free ORS key fixes this entirely.
- **Reddit blocked by browser**: shows as a small notice; doesn't break anything. Use Chrome/Firefox or lower Edge tracking prevention to "Basic".
- **Loop waypoints near coastlines**: app tries 8 candidate offsets and validates each on land before using it. If all fail (very rare), Round Trip falls back to retracing.

## Development

It's a single HTML file with no build step. Edit `index.html` directly, refresh the browser. The code is roughly:

- Lines 1–500: HTML and CSS
- Lines 500–700: Map setup, geocoding UI, stops management
- Lines 700–1000: API integrations (NPS, Recreation.gov, Reddit, Overpass)
- Lines 1000–1330: Trip planning logic, day splitting, results rendering

External dependencies (loaded from CDN):
- [Leaflet 1.9.4](https://leafletjs.com/) for the map
- Google Fonts: Abril Fatface, Fraunces, DM Mono

## License

MIT. See `LICENSE`.

## Credits

Map data © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors.
Tiles © [CARTO](https://carto.com/attributions).
Park data © U.S. National Park Service (public domain).
Campground data © Recreation.gov (public domain).
Discussion content © respective Reddit users.

---

Built for road trips, not for production. Have fun out there.
