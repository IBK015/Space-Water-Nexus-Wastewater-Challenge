# Digital twin dashboard: fusing satellite data with Rijnland's station network

Follow-up to the first research note. The direction is now: keep Rijnland's existing water stations as the backbone, add satellite observations on top, and show everything together in one digital twin dashboard. This note covers precedents, the fusion logic, dashboard concepts, and what to ask about the station data. Items marked "verify" come from general knowledge, not from a page I read.

## 1. What already exists (and what nobody seems to have done)

Several Dutch and European precedents show that the jury will recognise the format, which helps, but also that "a digital twin dashboard" alone will not stand out.

The Imagem multi-agent digital twin prototype for waterschap WDODelta combines sensor data, satellite imagery (used for vegetation growth and water level changes), rainfall forecasts and water levels at control structures. It shows where the watercourse fails flood-resilience norms and where water levels look illogical. Its focus is water quantity and asset management, and the page lists water quality only as a future use.

Aa en Maas has been reported as the first waterschap to manage its ditches with a digital twin. The article itself was blocked when I tried to read it, so I only know the headline. It is worth a look before the event.

The ESA Global Water Quality Digital Twin is the closest match to your idea on the EO side. It joins satellite-derived water quality layers (30 of them, switchable on a globe), in-situ samples and machine learning, and lets the user click a location to read values. It is a marine and coastal product, not an inland network tool.

The Delfland pesticide dashboard is the best precedent for the value story. It combines the national monitoring network with extra local stations, lets users zoom to a polder, flags peaks above the norm, and shows year-on-year trends. A policy official said closing one previously unknown discharge pipe removed a persistent norm violation. That sentence is what Rijnland wants to be able to say about wastewater.

Pipedream-WQ (from the first note) shows the modelling side: a network model plus a Kalman filter that assimilates sensor data improves estimates at unmeasured locations.

The gap I see: the precedents either do quantity (WDODelta), work at sea (GWDT), or visualise stations without tracing (Delfland). A dashboard that ties stations, satellite and the channel network together to narrow down where a discharge entered is a genuine step beyond them. Keep the claim that modest.

## 2. What "digital twin" should mean in your prototype

A jury will accept a twin if it does three things: it mirrors the current state of the system from live or recent data, it lets you ask what-if questions, and it supports a decision. For a weekend, I would define the twin as a network of watercourse reaches, where each reach carries a state (latest evidence, from which source, how old) and the network can be traversed in time. You do not need a hydraulic solver. You need a graph, travel times, and honest confidence labels.

## 3. Fusion patterns, from simple to ambitious

**Pattern 1: shared timeline.** Show station readings and satellite-derived values for the same place on one time axis, with rain underneath. This is the baseline everyone can build, and it already makes disagreements between sources visible.

**Pattern 2: station bracketing.** With the channel graph, a station upstream that reads normal and a station downstream that reads abnormal bracket the reach where something entered. This needs no satellite at all, only topology and timestamps, and it is the most convincing single piece of logic I can offer. The satellite then refines the answer inside the bracketed stretch when the water is wide enough to see.

**Pattern 3: calibrated virtual sensors.** Match satellite overpass dates to station measurements, fit a simple relationship (for example turbidity against a band ratio), and test it by leaving one station out at a time. Where it holds, the satellite extends the station's reading along the water body. Where it fails, show that too, as a skill map. The number of matched dates may be small because of cloud and sampling gaps, so count matchups early.

**Pattern 4: anchored interpolation.** Use the satellite for the spatial pattern along a water body and the station for the absolute level. It is a practical way to say "the station reads X here, and the picture suggests the same water is higher toward the east arm."

**Pattern 5: timing inversion.** If stations report frequently enough to time an anomaly onset, test each candidate discharge point and release time by simulating travel down the graph and comparing predicted arrival at each station with what was observed. The best-matching candidates become the ranked source areas. A grid search over points and times is enough. This depends on continuous or frequent station data, so it is conditional on what you learn about the stations.

**Pattern 6: rain conditioning.** Split anomalies into those that follow rain and those that occur in dry weather. Rain-linked anomalies point toward overflows and runoff, and dry-weather anomalies are the more suspicious ones for illegal connections or discharges. This is my own reasoning, not a published rule, so present it as a screening heuristic and check it with the water authority.

## 4. Dashboard concepts

### Concept 1: the information-age map

For every reach, show how old the most recent observation is and where it came from (station, satellite, citizen report, none). With stations alone, most of the network is dark most of the time. Adding satellite passes lights up more of it. This gives you a measurable, defensible headline: the median information age with and without satellite data. It avoids claiming the satellite measures pollution accurately, and it speaks directly to the problem of weekly or monthly sampling. Along the bottom, an observation timeline shows station samples, satellite overpasses, cloud-free flags and rain, so the data gap is visible at a glance.

### Concept 2: incident replay

Pick a past event (ideally one Rijnland already knows the cause of) and replay it on the twin. The user scrubs the time slider, sees rain arrive, a station reading move, a satellite scene light up a water body, and the candidate source area shrink as evidence accumulates. This is the demo that produces the "I understand it" moment, and it doubles as validation if the cause was later confirmed. Ask the challenge owner for such a case.

### Concept 3: click-to-trace with what-if

Click a flagged reach, choose a date, and see the upstream network within the travel-time window, coloured by candidate score. The candidate score itself can draw on Rijnland's own discharge records where they exist: Lozingspunt carries a discharge volume and frequency field, and Overstortconstructie carries peak overflow volumes at set return periods, so an upstream point with a large, frequent, recently active discharge should rank and colour differently from one that's small, rare, or has no logged activity, rather than every upstream point getting flagged the same way. Then drop a synthetic pulse at any upstream point and watch it travel, with predicted arrival times at each station. Compare with observed timing. Label any synthetic scenario clearly as a simulation, and label the candidate colouring as relative likelihood among known points, never a confirmed source.

### Concept 4: early-warning tab

Use the KNMI radar nowcast (a verified open dataset: 5-minute precipitation forecast up to 2 hours ahead) to raise the risk of reaches downstream of overflows and known sensitive points, schedule attention for the next satellite pass, and produce a priority list for the sampling team. The output is a ranked shortlist, not an alarm claiming pollution.

### Layout suggestion

Map in the centre with a layer switcher (stations, satellite indices, information age, candidate score, rain). A time slider with the observation ticks underneath. A right-hand evidence card for the selected reach with the station chart and its anomaly band, the satellite time series for the nearest water body, rain bars, and a plain-language confidence label. An event list on the left. Keep the wording cautious: "possible pollution signal," "candidate source area."

## 5. How the station data changes the design

Since you will learn more about the stations soon, here is what each type of data enables.

| If the stations give... | Then you can do... | Watch out for |
|---|---|---|
| Frequent (sub-daily) sensor readings such as conductivity, temperature, oxygen or turbidity | Anomaly detection, bracketing, timing inversion, rain conditioning | Sensor drift and gaps; ask for QA flags |
| Lab grab samples every few weeks | Calibration and validation of satellite proxies, long-term baselines, sampling prioritisation | Few matchups with clear overpasses |
| Flow or level at pumping stations and weirs | Better travel times, flow direction and event context | Direction can reverse when pumps switch |
| Coordinates and station-to-watercourse links | Placing stations on the graph, which bracketing needs | Stations may not snap cleanly to the network |

For anomaly detection on continuous data, start simple: a rolling seasonal baseline and a robust deviation score (median-based, so single spikes do not distort it). Published open-source options exist (US EPA CANARY appears in the literature; I could not open its documentation, so verify before relying on it). Machine learning is not needed for the first version.

## 6. Satellite side: practical additions

The Sentinel Hub Statistical API at the Copernicus Data Space Ecosystem returns statistics for an area and time window as JSON without downloading images. That fits a dashboard well: define a polygon per water body, request a time series of a chosen index, and feed it straight into the chart. Custom scripts for water body masking (WBM) and aquatic plants and algae (APA) exist in the Sentinel Hub script library. Duckweed on small rivers has been studied with Sentinel-2 vegetation and water indices in a Portuguese case, so a duckweed flag is realistic and would remove a major false-positive source in Dutch ditches. Treat the pixel-size limit from the first note as still binding: restrict satellite claims to the wider water bodies and show that restriction on the map.

Rijnland's watercourse and structure geometry is likely available through the waterschap data model (DAMO, maintained by Het Waterschapshuis) and an INSPIRE hydrography dataset for waterschappen is listed on data.overheid.nl. Confirm which layers Rijnland exposes and whether the flow direction is encoded.

## 7. Build approach

Precompute in Python (geopandas, networkx, rasterio) and serve static files to a map front end. Section 10 below sets out a specific option, GeoLibre, that can take the front-end half of this off the build list almost entirely. Charts with ECharts or Plotly if going custom, or GeoLibre's own charting if using that route. If the team is Python-only and time is short, Streamlit with pydeck is a fallback. Grafana with a map panel is an option if the station time series dominate. A static, precomputed demo cannot fail on stage, so I recommend it, with the pipeline described honestly as the path to live operation.

Prepare before the event with mock stations and a synthetic incident on the real network, so the trace, timing and dashboard are working when the real data arrives. Swap the adapters, not the design.

## 8. Recommended shape

Lead with the information-age map (the gap, made visible), show incident replay as the core demo, and let click-to-trace and station bracketing deliver the source-narrowing moment. Use the satellite where it earns its place: triggered looks after rain or station anomalies, calibrated extension on wider water, and context layers for runoff and vegetation. Close with the early-warning shortlist for the sampling team and a clear limitations slide.

## 9. Questions to bring back about the stations

Which parameters, and how often is each measured? Are readings continuous, and with what latency? How many stations, with coordinates and the watercourse each sits on? How long is the history? Are there QA flags, and how is drift handled? Is there an API or only file exports? Which stations measure flow or level? Are there permitted discharge points, treatment plant outfalls and overflows as a layer? Is there one past incident with a known cause we can replay?

## 10. GeoLibre as a ready-made front end

GeoLibre (geolibre.app) is a free, open-source browser GIS built on MapLibre GL, DuckDB-WASM Spatial and deck.gl. It runs entirely client-side, no server, no install, and it can load a WMS, WFS, ArcGIS REST endpoint or STAC catalog directly by URL. That last point matters here specifically: our cloud workspace and the linked device are both blocked from reaching Rijnland's map server, but the user's own browser is not. Loading Rijnland's layers straight into GeoLibre, by URL, sidesteps the download problem for exploration, even though the actual submission should still run on data we've pulled and versioned ourselves.

Matched against the concepts and layout above, GeoLibre already has most of the pieces built:

The layer switcher in the layout suggestion (section 4) is GeoLibre's layer stack, with grouping and rule-based styling already built in, so the candidate-score and information-age colour ramps from concept 1 can be set up as a graduated or expression-based renderer rather than custom code.

The time slider in concept 2 (incident replay) is a built-in plugin, not something to write. Scrubbing through a precomputed incident, station reading moving, satellite scene lighting up, candidate area shrinking, is close to a native feature rather than a custom animation.

Click-to-trace (concept 3) needs an attribute form or pop-up wired to the selected reach, plus the editable attribute table GeoLibre already has, so the "evidence card" in the layout (station chart, satellite series, rain bars, confidence label) can be built from its existing attribute and charting panels instead of a bespoke sidebar component.

The Spectral Index toolbox (NDVI, NDWI, EVI presets) and 1,000-plus Whitebox processing tools run client-side, useful if Sentinel-2 tiles get loaded directly rather than precomputed indices only.

DuckDB Spatial SQL in the browser could run the station-bracketing query (pattern 2) live, on stage, letting someone type a slightly different question during Q&A and get a real answer rather than a canned one. The timing-inversion grid search (pattern 5) is still better done as a Python precompute step; that's a graph-traversal search, not a spatial join, and doesn't belong in a live SQL box.

The story map builder, with standalone HTML export, is a plausible home for the pitch narrative itself: walk a judge through problem, data gap, and the incident replay, in the same tool that holds the working prototype, rather than a separate slide deck.

What it doesn't replace: the actual detection and tracing logic. Anomaly scoring, the network graph, and the timing-inversion search still need to be built in Python and exported as GeoJSON, GeoParquet or a small feature service for GeoLibre to display. GeoLibre is the visualisation and interaction layer from section 7's build approach, not a substitute for the processing and detection stages upstream of it.

It's also a young, community project rather than something with a long track record, so before committing the whole prototype to it: spend perhaps thirty minutes loading two or three real Rijnland layers by URL (Lozingspunt, the watercourse register, one Meetlocatie layer) to check that styling, popups and performance hold up, and keep a plain MapLibre or Streamlit fallback in mind in case it doesn't behave well live. If the spike goes well, it likely cuts real hours off the front-end build, hours that go straight into the anomaly-detection and network-tracing logic, which is what the challenge is actually judging.

## Sources

- [Imagem: multi-agent digital twin for waterschappen](https://www.imagem.nl/blog/sturen-op-waterbeheer-met-multiagent-digital-twin/)
- [H2O: Aa en Maas manages ditches with a digital twin (headline only, page blocked)](https://www.h2owaternetwerk.nl/h2o-actueel/aa-en-maas-beheert-sloten-als-eerste-met-een-digital-twin)
- [ESA Global Water Quality Digital Twin](https://business.esa.int/projects/global-water-quality-digital-twin-gwdt)
- [Glastuinbouw Waterproof: Dashboard Waterkwaliteit](https://www.glastuinbouwwaterproof.nl/nieuws/nieuwe-visualisatietool-dashboard-waterkwaliteit/)
- [Digital twin model for contaminant transport in drainage networks](https://www.sciencedirect.com/science/article/pii/S1364815223002542)
- [HDSR monitoring networks (typical waterschap setup)](https://www.hdsr.nl/werk/werken-we-samen/meten-waterkwaliteit/meetnetten/)
- [KNMI 5-minute radar nowcast dataset](https://dataplatform.knmi.nl/dataset/access/radar-forecast-2-0)
- [Sentinel Hub APIs at Copernicus Data Space](https://dataspace.copernicus.eu/analyse/apis/sentinel-hub)
- [Sentinel Hub water bodies mapping script](https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/water_bodies_mapping-wbm/)
- [Sentinel Hub aquatic plants and algae script](https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/apa_script/)
- [Duckweed monitoring in small rivers with Sentinel-2](https://www.mdpi.com/2073-4441/14/15/2284)
- [Waterschappen hydrography INSPIRE dataset](https://data.overheid.nl/en/dataset/44273-waterschappen-hydrografie-inspire)
- [DAMO HydroObject handbook](https://damo.hetwaterschapshuis.nl/DAMO%201.4/Objectenhandboek%20DAMO%201.4/HTML/HydroObject.html)
- [Anomaly detection in high-frequency water-quality sensor data](https://www.sciencedirect.com/science/article/abs/pii/S0048969719305662)
- [GeoLibre](https://geolibre.app/)
