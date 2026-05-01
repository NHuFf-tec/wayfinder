# Changelog

## [0.2.0] — 2026-04-30

### Added
- National Park Service API integration (with user-supplied free key)
- Recreation.gov (RIDB) integration for federal campgrounds
- Reddit integration: pulls top traveler discussions per stop from r/roadtrip, r/NationalParks, r/CampingandHiking, r/travel
- Reddit r/StateParks discussions for states crossed
- API Keys modal with localStorage persistence
- State parks linkout section with official sites for all 50 states
- TripAdvisor and Reddit deep-search buttons on every recommended stop
- Topographic map layer toggle (OpenTopoMap)
- Distance-off-route shown for each pick (e.g., "18 mi off route")

### Changed
- Switched map tiles from OSM main server to CARTO Voyager (OSM blocks `file://` access; CARTO works without referer header)
- States-along-route now uses reverse geocoding sample points to catch fly-over states
- Loading messages now step through actual phases of the request

### Fixed
- HTML escaping on all third-party content (Reddit titles, OSM names, NPS descriptions) to prevent XSS
- `target="_blank"` links now include `rel="noopener"` for tabnabbing protection

## [0.1.0] — 2026-04-30

### Initial release
- Multi-stop routing with autocomplete
- Day-by-day itinerary generation
- POI discovery via Overpass API
- Vintage paper-map aesthetic with Leaflet
