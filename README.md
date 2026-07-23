# 2026 West Virginia Flooding — Heritage Building Exposure Map

Public map tracking heritage-building exposure to the catastrophic flash flooding that hit north-central
West Virginia July 21-22, 2026 (Governor Morrisey declared a state of emergency for all 55 counties on 7/21).

Live at: https://rkn2.github.io/2026wvflood-heritage-map/ *(once published)*

## What this shows (first pass — gauges + heritage only)

- **15 NRHP-listed properties/districts** in Upshur, Lewis, Harrison, and Pleasants counties — the
  counties hardest hit by this event.
- **3 river gauges** (Buckhannon River at Buckhannon, West Fork River at Weston, West Fork River near
  Clarksburg) with live NOAA National Water Prediction Service (NWPS) flood-category data.

Each heritage building/district is colored by the **peak flood category recorded so far at its nearest
gauge** — this is a proximity/exposure indicator, not a confirmed damage assessment. No building here
has been field-verified for actual damage. Properties with no nearby gauge in this dataset are shown gray
with a dashed outline — that means "unknown," not "unaffected."

## Data sources

| Layer | Source | Retrieved |
|---|---|---|
| Heritage building/district boundaries | NPS NRHP GIS ArcGIS REST service (`mapservices.nps.gov/arcgis/rest/services/cultural_resources/nrhp_locations`), layers 0 (points) and 1 (polygons), filtered to Upshur/Lewis/Harrison/Pleasants counties, WV | 2026-07-23 |
| River gauge flood category + stage/time series | NOAA NWPS API (`api.water.noaa.gov/nwps/v1/gauges/{id}`), gauges `bknw2`, `wtnw2`, `clkw2` | 2026-07-23 |

Full research pipeline and raw data lives in the private `steer` repo at
`events/2026WVflood/data/{heritage,disaster}/`; this repo holds only the public-safe stripped copies
(`heritage.geojson`, `gauges.geojson`).

## Known gaps / next steps

- **Pleasants County (St. Marys)** has no river gauge in this dataset — the flooding there was on Duck
  Creek and the Little Muskingum River, neither of which has an NWS/USGS real-time gauge. The Cain House
  and Pleasants County Courthouse are shown with unknown exposure.
- **No event-specific flood extent polygon yet.** Unlike an earthquake ShakeMap, there's no single
  authoritative flood-extent layer for this event. USGS Short-Term Network (STN) high-water marks
  (`stn.wim.usgs.gov`) will eventually provide surveyed extent data, but field crews haven't uploaded
  results yet as of 2026-07-23 (typically a days-to-weeks lag after crest).
  FEMA NFHL / `mapwv.gov/flood/` shows the static 100-/500-year floodplain, not this event's actual extent.
- **No damage reports yet** — this is 2 days into the event; no StEER PVRR or field survey exists.
  Local news (WV MetroNews, mybuckhannon.com, The Real WV, WDTV) has specific damage photos (e.g. a
  Buckhannon Walmart submerged, a high-water mark on a Weston hotel lobby door) that could be manually
  geocoded as a "media-reported" tier in a future pass.
- SAR (Sentinel-1) change detection was considered but deprioritized for this pass — these are flashy
  small-creek floods that crest and recede in ~24-48 hrs, so unless a Sentinel-1 pass happened to land
  during peak inundation, SAR would likely only show residual soil saturation, not the crest extent.
  Worth checking actual scene dates via ASF Vertex before investing in this.

Built with [Leaflet](https://leafletjs.com/) + OpenStreetMap tiles, no build step — just `index.html`,
`heritage.geojson`, and `gauges.geojson`.
