# 2026 West Virginia Flooding — Heritage Building Exposure Map

Public map tracking heritage-building exposure to the catastrophic flash flooding that hit north-central
West Virginia July 21-22, 2026 (Governor Morrisey declared a state of emergency for all 55 counties on 7/21).

Live at: https://rkn2.github.io/2026wvflood-heritage-map/

## What this shows

- **13 NRHP-listed historic districts/properties**, rendered as **3,151 individual building
  footprints** (plus 2 individually-listed properties shown as points), across Upshur, Lewis, Harrison,
  and Pleasants counties — the counties hardest hit by this event.
- **3 river gauges** (Buckhannon River at Buckhannon, West Fork River at Weston, West Fork River near
  Clarksburg) with live NOAA National Water Prediction Service (NWPS) flood-category data.
- **12 media-reported damage points** (black `!` markers) — specific locations named in local news
  coverage: flooded stores, impassable roads/highway segments, a hospital that cancelled appointments,
  the Upshur County Courthouse closure.

Each heritage building is colored by the **peak flood category recorded so far at its district's nearest
gauge** — this is a proximity/exposure indicator, not a confirmed damage assessment. No building here
has been field-verified for actual damage. Buildings in districts with no nearby gauge in this dataset
are shown gray with a dashed outline — that means "unknown," not "unaffected." The damage points are a
separate, independent layer — pulled directly from news reporting, not inferred from gauge proximity.

## Data sources

| Layer | Source | Retrieved |
|---|---|---|
| Heritage district identification (which places, which counties) | NPS NRHP GIS ArcGIS REST service (`mapservices.nps.gov/arcgis/rest/services/cultural_resources/nrhp_locations`), layers 0 (points) and 1 (polygons), filtered to Upshur/Lewis/Harrison/Pleasants counties, WV | 2026-07-23 |
| Heritage building **geometry** (the actual shapes drawn on the map) | OpenStreetMap building footprints via the Overpass API, queried within each NRHP district's bounding envelope (see below) | 2026-07-23 |
| River gauge flood category + stage/time series | NOAA NWPS API (`api.water.noaa.gov/nwps/v1/gauges/{id}`), gauges `bknw2`, `wtnw2`, `clkw2` | 2026-07-23 |
| Damage report locations | Local news: My Buckhannon, WDTV, AP wire (via CBS News/NBC News), CNN — see each point's popup for its specific article link | 2026-07-23 |
| Damage report coordinates | OpenStreetMap Nominatim geocoder, from street addresses / named landmarks in the articles | 2026-07-23 |

Full research pipeline and raw data lives in the private `steer` repo at
`events/2026WVflood/data/{heritage,disaster}/`; this repo holds only the public-safe stripped copies
(`heritage.geojson`, `gauges.geojson`, `damage_reports.geojson`).

### Damage report caveats

- **Media-reported, not field-verified.** These are locations named in news coverage as flooded/closed/
  damaged — no StEER field survey or official damage assessment exists yet for this event (it's 2 days
  old). Treat as "worth checking," not "confirmed damage."
- **One point is a best-guess location, flagged as such.** The hotel with the widely-photographed
  high-water-mark lobby door was never named in any article found — it's placed near the Weston Walmart/
  I-79/US-33 interchange where multiple hotels cluster, since that's as precise as the sourcing allows.
- **Coverage is uneven, not a damage census.** These 12 points reflect what made it into news coverage
  in the first 48 hours, weighted toward Buckhannon (where the most detailed local reporting — My
  Buckhannon — is based). Absence of a point elsewhere is not evidence of no damage.

### Why building footprints instead of the NPS district polygon

NPS's own NRHP GIS metadata warns that its district polygons are built by connecting the raw UTM corner
coordinates submitted on each nomination form, and are prone to "transcription errors and incorrect
sequencing." We hit this directly: the Downtown Buckhannon Historic District polygon from NPS is a
self-intersecting bowtie (4 points, wrong winding order), not a usable boundary.

Instead, each district's NPS polygon is now used only as a rough envelope (padded ~15%, minimum ~90m) to
query OSM for real building footprints inside it — those buildings are what's actually drawn on the map,
one polygon per building, all tagged with their parent district's name/county/gauge. This trades "one
tidy (but wrong) shape" for "many small shapes that are individually accurate."

One exception: **Weston State Hospital** (Trans-Allegheny Lunatic Asylum) uses its own OSM way, tagged
directly with `ref:nrhp=78002805` — a hand-traced 153-point outline of the actual building, more precise
than a bbox-derived footprint would be.

Building counts per district (13 districts, 3,151 buildings total): Weston Downtown Residential (1,412),
Buckhannon Central Residential (730), Downtown Buckhannon (278), Clarksburg Downtown (195), Weston
Downtown (179), Shinnston (133), Salem (79), Glen Elk (58), Jackson's Mill 4-H Camp (58), Annamede (8),
Quality Hill (15), May-Kraus Farm (5), Weston State Hospital (1 — the asylum building itself).

## Known gaps / next steps

- **Pleasants County (St. Marys)** has no river gauge in this dataset — the flooding there was on Duck
  Creek and the Little Muskingum River, neither of which has an NWS/USGS real-time gauge. The Cain House
  and Pleasants County Courthouse are shown with unknown exposure.
- **No event-specific flood extent polygon yet.** Unlike an earthquake ShakeMap, there's no single
  authoritative flood-extent layer for this event. USGS Short-Term Network (STN) high-water marks
  (`stn.wim.usgs.gov`) will eventually provide surveyed extent data, but field crews haven't uploaded
  results yet as of 2026-07-23 (typically a days-to-weeks lag after crest).
  FEMA NFHL / `mapwv.gov/flood/` shows the static 100-/500-year floodplain, not this event's actual extent.
- **No field-verified damage assessment yet** — the 12 damage points are media-reported only (see
  caveats above); no StEER PVRR or official damage assessment exists yet for this 2-day-old event.
- SAR (Sentinel-1) change detection was considered but deprioritized for this pass — these are flashy
  small-creek floods that crest and recede in ~24-48 hrs, so unless a Sentinel-1 pass happened to land
  during peak inundation, SAR would likely only show residual soil saturation, not the crest extent.
  Worth checking actual scene dates via ASF Vertex before investing in this.

Built with [Leaflet](https://leafletjs.com/) + OpenStreetMap tiles, no build step — just `index.html`,
`heritage.geojson`, `gauges.geojson`, and `damage_reports.geojson`.
