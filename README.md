# 2026 West Virginia Flooding — Heritage Building Exposure Map

Password-gated map tracking heritage-building exposure to the catastrophic flash flooding that hit
north-central West Virginia July 21-22, 2026 (Governor Morrisey declared a state of emergency for all 55
counties on 7/21). Shared within the research group only, not intended as a public map.

Live at: https://rkn2.github.io/2026wvflood-heritage-map/ (password required)

**Access note:** the password gate is a client-side JS check only (see `index.html`) — enough to keep
this out of search engines and casual link-following, but anyone who views the page source can read the
password. GitHub Pages does not serve from private repos on the free plan (confirmed by testing — the
site 404'd immediately), so **this repo is public**; the source, README, and every data/image file are
directly readable/downloadable by URL regardless of the password. This is a "keep it out of casual
reach" measure, not real access control.

**Nearmap imagery included at Becca's explicit direction, with that limitation acknowledged.**
`nearmap_weston_core.jpg`, `nearmap_jacksons_mill.jpg`, `nearmap_clarksburg_core.jpg`, and
`nearmap_buckhannon_core.jpg` are actual licensed Nearmap aerial photos (not open data like everything
else in this repo), placed as georeferenced image overlays. Given the access note above, this is not
meaningfully different from posting them publicly — treat this repo as containing real proprietary
imagery that happens to have a password on the pretty version.

## What this shows

- **13 NRHP-listed historic districts/properties**, rendered as **3,151 individual building
  footprints** (plus 2 individually-listed properties shown as points), across Upshur, Lewis, Harrison,
  and Pleasants counties — the counties hardest hit by this event.
- **6 river gauges** (Buckhannon River at Alton and at Buckhannon; West Fork River at Walkersville,
  Weston, near Clarksburg, and at Enterprise) with live NOAA National Water Prediction Service
  (NWPS) flood-category data. Alton and Walkersville sit upstream of Buckhannon and Weston
  respectively and never reached flood stage this event — useful upstream/downstream context.
- **16 media-reported damage points** (black `!` markers) — specific locations named in local news
  coverage: flooded stores, impassable roads/highway segments, a hospital that cancelled appointments,
  the Upshur County Courthouse closure, and a cluster of suspected-tornado damage in Pleasants County
  (a separate hazard from the same storm system — flagged as such in each point's popup, not flooding).
- **Preliminary Nearmap imagery-awareness layer** (blue dashed cells) — grid cells where Nearmap has
  post-event aerial imagery on file for this event ("North-Central West Virginia Flood," captured
  2026-07-22). This is a coverage indicator only — it tells you where recent aerial imagery *exists*,
  not what it shows or whether damage occurred. The actual Nearmap imagery is licensed/proprietary and is
  NOT included here or anywhere in this repo — only the fact of coverage (derived from Nearmap's
  Coverage API metadata) is shared.

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
| River gauge flood category + stage/time series | NOAA NWPS API (`api.water.noaa.gov/nwps/v1/gauges/{id}`), gauges `altw2`, `bknw2`, `wlkw2`, `wtnw2`, `clkw2`, `entw2` — found via a county-wide USGS Water Services sweep, not just name-search; see `overall/data-scoping-methodology.md` in the private repo | 2026-07-23 |
| Damage report locations | Local news: My Buckhannon, WDTV, WV MetroNews, The Vindicator, AP wire (via CBS News/NBC News), CNN — see each point's popup for its specific article link. Discovery pass (2026-07-24) used a Google News RSS scan (`tools/scan_media.py` in the private repo), not manual search. | 2026-07-24 |
| Damage report coordinates | OpenStreetMap Nominatim geocoder, from street addresses / named landmarks in the articles | 2026-07-23 |
| Nearmap imagery-awareness cells | Nearmap Coverage API (`api.nearmap.com/coverage/v2/point/...`), sampled on a ~1.3km grid over Weston/Clarksburg/Buckhannon, filtered to surveys tagged `postCatEventId` for this flood event. Metadata only — no imagery pixels included. | 2026-07-23 |
| Nearmap flood-day imagery (4 mosaics) | Nearmap Tile API (`Vert` layer, zoom 19), disaster survey "North-Central West Virginia Flood." Weston/Jackson's Mill/Clarksburg captured 2026-07-22 evening (`until=2026-07-22`); Buckhannon captured 2026-07-23 morning (`until=2026-07-23`, surveys `captureDateTime` 11:19 & 12:52 UTC, same `postCatEventId`). Actual licensed imagery, not open data — see access note above. | 2026-07-24 |

Full research pipeline and raw data lives in the private `steer` repo at
`events/2026WVflood/data/{heritage,disaster}/`; this repo holds only the public-safe stripped copies
(`heritage.geojson`, `gauges.geojson`, `damage_reports.geojson`, `nearmap_coverage.geojson`).

### Nearmap coverage caveats

- **Coverage findable ≠ damage confirmed.** A blue cell (or an image overlay) means Nearmap flew that spot
  after the flood — nothing more. Always check the actual imagery before drawing conclusions.
- **Deployment rolled out over multiple days and did not track flood severity at first.** As of
  2026-07-23 (this event's first pass), Buckhannon — the worst river gauge reading of the whole
  event, record major flood stage, first time in ~80 years — had **zero** disaster-response
  coverage, while Weston (only "minor" gauge category) got the largest flown footprint. **Update,
  same day (2026-07-23, later check):** Nearmap flew Buckhannon too — two new surveys captured
  that morning (`captureDateTime` 2026-07-23T11:19–12:52 UTC), tagged to the same flood event ID
  (`postCatEventId aeeed8d5-ddc5-58b2-b5ff-b47711b9edb0`). So the "doesn't track severity" finding
  was real but time-bound: Buckhannon got flown, just a day later than Weston/Clarksburg. Treat
  "was it flown yet" as a moving target for a rolling disaster-response deployment, not a
  one-time fact — re-check the Coverage API before concluding a place has no coverage.
- **Building-level coverage check, confirmed exhaustively (updated 2026-07-23).** Checked Nearmap's
  Coverage API against representative points in each of the 15 districts, filtering specifically for
  surveys tagged to this flood event's `postCatEventId` (not just any recent capture date, which can
  produce false positives from routine, non-disaster survey flights). Result — each `heritage.geojson`
  feature now carries a `nearmap_flood_coverage` boolean reflecting this:
  - **Covered (9 districts / properties, 2,926 of 3,153 buildings):** Buckhannon Central Residential,
    Downtown Buckhannon *(both newly covered as of the 2026-07-23 morning flight — not covered in the
    original 2026-07-22 pass)*, Weston Downtown Residential, Weston Downtown, Weston State Hospital,
    Jackson's Mill 4-H Camp, Clarksburg Downtown, Quality Hill, Glen Elk.
  - **Not covered (6 districts / properties, 227 buildings):** Shinnston, Salem, Annamede, May-Kraus
    Farm, Cain House, Pleasants County Courthouse.
  - **Buckhannon mosaic now fetched (2026-07-24)** — 1,332 zoom-19 tiles covering both Buckhannon
    districts, `until=2026-07-23`, 100% tile-fill (no gaps). Unlike Weston's mosaic, the Buckhannon
    imagery shows **no obvious flood signal** — no visibly muddy water in the river channel, no
    standing water in streets, no visible debris at this resolution. This is plausibly consistent
    with the timeline (Buckhannon's river crested 7/21-22 and was already well into recession by the
    7/23 morning flight, a day-plus after Weston's capture caught active muddy floodwater) but has
    not been otherwise confirmed — treat "imagery looks normal" as inconclusive, not as evidence the
    flooding there was less severe. Buckhannon had the worst gauge reading of the whole event.
- **Blue-cell awareness layer is grid-derived (approximate edges) and reflects the original
  2026-07-22 sweep** — it has not been regenerated to include the new 2026-07-23 Buckhannon
  surveys, so it will currently under-represent coverage there relative to the
  `nearmap_flood_coverage` field on the heritage layer.
- **Clarksburg's mosaic is significantly cloud-obscured** in large sections (off by default in the
  layer toggle for that reason) — coverage existing doesn't mean the imagery is usable there. Weston
  downtown, Jackson's Mill, and Buckhannon downtown all came through clear (no clouds).

### Damage report caveats

- **Media-reported, not field-verified.** These are locations named in news coverage as flooded/closed/
  damaged — no official field damage assessment exists for this event (there is no StEER deployment
  here; this is independent research, not an organized field response). Treat as "worth checking,"
  not "confirmed damage."
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
- **No field-verified damage assessment** — the 16 damage points are media-reported only (see
  caveats above); there is no StEER deployment for this event and no other agency's official damage
  assessment has been published either.
- **SAR (Sentinel-1) checked and unavailable.** ASF catalog search confirmed no Sentinel-1 acquisition
  over this AOI between 2026-07-15 and 2026-07-23 — a real coverage gap, not a processing choice. Nearmap
  turned out to be the usable substitute (see above).
- Clarksburg's cloud-obscured Nearmap capture means we still don't have clear recent imagery for that
  town's river corridor specifically.
- Shinnston, Salem, Annamede, May-Kraus Farm, Cain House, and Pleasants County Courthouse still have
  zero recent aerial imagery of any kind for this event (no Sentinel-1, no Nearmap) — gauge exposure
  and media reports are the only signal available for those places right now.

Built with [Leaflet](https://leafletjs.com/) + OpenStreetMap tiles, no build step — just `index.html`,
`heritage.geojson`, `gauges.geojson`, `damage_reports.geojson`, `nearmap_coverage.geojson`, and the 3
Nearmap imagery mosaics.
