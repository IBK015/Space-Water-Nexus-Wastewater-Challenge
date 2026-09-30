# Granular insights into wastewater discharges: research and brainstorm

Challenge owner: Water Natuurlijk Rijnland. Event: Space & Water Nexus Hackathon, 1 to 3 October 2026.

This note has three parts: what the research established, a set of concept ideas built from the four themes we discussed (digital twin, EO interrogation, trigger-based impact analysis, early warning), and a recommended prototype with a prep plan. Where a statement comes from a page I actually read, I say so. Where it comes from general knowledge, it is marked "verify".

## 1. What the research established

### The problem as the challenge owner states it

Rijnland samples water quality at more than 100 locations, at intervals that run from every two weeks to once every three years depending on the parameter. Samples are taken by hand and analysed in a lab. Bathing-water sites are tested every two weeks in season. Beyond that, the authority relies on reports from the public when a spot looks or smells bad. The result is that a discharge can happen, pass, and leave no trace in the dataset. Nobody can say what happened, when, or where it started.

The expected output is "a map or simulator that visualises how water flows or water-quality conditions change in (near) real time." So the jury wants something visual and interactive, not a paper analysis.

### What Sentinel-2 can and cannot do here

Sentinel-2 has bands at 10 m (4 bands), 20 m (6 bands) and 60 m (3 bands). Two satellites give a 5-day revisit, and three satellites give 2 to 3 days at mid-latitudes. The Copernicus Data Space page I read described a three-satellite setup running through 13 March 2026, so check what is flying now. Data is reachable through STAC, OData, Sentinel Hub and openEO.

The parameters that reviews and case studies treat as feasible are chlorophyll-a, turbidity and suspended matter. A commonly used script (Se2WaQ on Sentinel Hub) computes chlorophyll, cyanobacteria, turbidity, CDOM, DOC and water colour from simple band ratios such as B03/B01 and B03/B04. These are empirical formulas, and their accuracy in narrow, shallow, brown Dutch ditches is uncertain.

The limits matter more than the capabilities for this challenge. A typical polder ditch is a few metres wide, so a 10 m pixel is mostly bank, reed and duckweed. Cloud cover removes many of the overpasses. A Los Angeles study that tracked turbidity after storms with Sentinel-2 reached R² of 0.45 with plain regression and 0.63 with random forest, and its authors said the method works best next to field sampling, not on its own. Duckweed and floating vegetation will look like "anomalies" and swamp any subtle signal.

My own inference from this: satellites will not pinpoint a discharge into a ditch. They can work on wider water (canals, the boezem, lakes, the stretches near large outfalls), and they can supply context (rain-driven runoff, bare fields, vegetation state, surface wetness) that tells you where and when to look. That is a defensible and honest claim, and a stronger one than "we detect illegal discharges from space."

### Other useful facts

Sentinel-1 radar sees through cloud, which makes it a good companion to Sentinel-2. It does not measure water quality. It can show surface roughness, floating films that damp the surface, and wetness. Published oil-slick work is almost all marine, so treat inland use as unproven (verify).

Landsat thermal imagery has been used to map effluent plumes on coasts and rivers. Its native thermal resolution is about 100 m (general knowledge, verify), which is too coarse for ditches but could matter at a large treatment plant outfall.

Pipedream-WQ, a published digital twin for contaminant transport in drainage networks, couples a network model of flow and transport with a Kalman filter that assimilates live sensor data. Its finding is useful for us: assimilating sensor data improves concentration estimates at ungauged locations compared with the model alone. That is exactly the "fill the gaps between sparse measurements" story, at hackathon scale.

Rijnland already runs a Databureau Waterketen with municipalities and drinking-water companies. It covers impervious surface mapping, unconnected properties, sewer service areas and stormwater intrusion into sewers. The page did not say which overflow or treatment-plant data it holds, so ask the challenge owner.

Netherlands precedent exists. Sentinel-2 and Sentinel-3 are already used by Dutch water boards (Noorderzijlvest is the named example) to assess lakes beyond what sampling gives.

### What I could not verify

The Open Data Space Lab page returned no readable content. The Rijnland ArcGIS filter gallery is a JavaScript app that I could not read from here, so open it in your browser and list the layers it exposes. The Satellietdataportaal page confirms a viewer, a STAC API with WMTS/XYZ tiles, and FTP access for optical data, but not the products, resolution or revisit. Ask whether it carries commercial high-resolution imagery, since that would change the ditch problem. The Rijnland news page on data and AI returned a 404, so I have no confirmed detail on their internal AI work.

## 2. Data inventory

| Data | What it gives us | Status |
|---|---|---|
| Sentinel-2 L2A (CDSE) | Turbidity, chlorophyll and colour proxies, vegetation, land state; 10 to 60 m | Verified |
| Sentinel-1 GRD (CDSE) | Cloud-free surface wetness and roughness context | Verified as available, use case is my inference |
| Satellietdataportaal (STAC, WMTS) | Pre-processed Dutch imagery, possibly higher resolution | Product detail unverified |
| Rijnland ArcGIS layers | Watercourses, structures, likely sampling points and discharge locations | Need to inspect in browser |
| Rijnland sampling data | Ground truth for calibration, at coarse intervals | Confirmed to exist, access via challenge owner |
| Rainfall (KNMI radar and stations) | The main trigger for overflows and runoff | General knowledge, verify access route |
| Land use and topography (PDOK: BGT, Top10NL, crop parcels, AHN) | Agriculture, greenhouses, paved area, and flow direction on land | General knowledge, verify |
| Citizen reports | Weak but useful point evidence with timestamps | Confirmed to exist, ask for an extract |
| Discharge permits, treatment plants, overflows | Candidate source points | Ask challenge owner, this is likely the key dataset |

The data the challenge owner shares on Discord and on site will matter more than anything on this list. Expect it to include something we have not thought of.

## 3. Concept ideas

### A. Trigger-based EO interrogation ("something happened, now go look")

Instead of scanning everything all the time, define triggers and let each one launch a targeted satellite look. A trigger might be a heavy rain event from radar, a sensor reading crossing a threshold, a citizen report, or a change in a pumping station's discharge. When a trigger fires, the system pulls the first usable Sentinel-2 scene after the event, computes the anomaly against a seasonal baseline for the same water body, and shows before and after. This flips the cloud-cover problem into a feature: you only need one good scene per event, not a continuous record.

Why it works for the demo: it produces a clear before/after image pair with a stated reason for looking. Why it is honest: the satellite is used as supporting evidence tied to a known cause window.

### B. Digital twin, lite version

A full hydraulic twin is out of scope. A network twin is not. Build a directed graph of watercourses from Rijnland's layers, with flow direction taken from structures (pumping stations, weirs, culverts), and give each reach a length and a rough velocity. That lets you do two things quickly. Going downstream, you can simulate how a pulse released at a point moves and when it arrives at each sampling location. Going upstream, from an anomaly, you can list every reach and every candidate discharge point that could have contributed within a given travel time.

The velocity numbers will be crude, and I would show travel-time bands (fast, medium, slow scenarios) instead of a single answer. That communicates the uncertainty visually.

### C. Source-area scoring ("where should we look first?")

For each upstream reach, compute a transparent score from a few ingredients: proximity in travel time to the anomaly, presence of permitted outfalls or overflows, land use (paved area, agriculture, greenhouses), rain in the preceding hours, and any citizen reports. The output is a ranked list of candidate source areas, with the weights visible and adjustable. No machine learning is needed, and non-technical stakeholders can follow the logic. Call the outputs "candidate source areas" throughout.

### D. Early warning as a sampling-prioritisation tool

This is the idea I would lead with in the pitch. Rijnland's actual constraint is that samplers cannot be everywhere. An early-warning layer that combines rain, satellite anomalies, sensor readings and network position into a risk colour per reach tells the team where to send a sampler tomorrow morning. The satellite does not need to be right about the pollutant. It needs to be better than random at choosing where to sample, and that is easy to evaluate against their historical samples.

### E. Virtual sensors between sampling points

Use the >100 sampling stations as ground truth. Regress simple satellite features (band ratios, indices) against measured turbidity, transparency or chlorophyll where the water is wide enough, using held-out stations to test it. Report the error honestly. Even a modest result (say, useful on lakes and canals, not on ditches) is a real finding for the challenge owner, and it tells them where satellite monitoring is worth pursuing.

### F. Runoff-risk layers from EO

Sentinel-2 vegetation state and bare soil, plus Sentinel-1 wetness, can flag fields likely to shed sediment and nutrients after rain. This does not detect wastewater, but it helps separate agricultural runoff from point-source discharge, which reduces false leads. It also gives the map more visual richness.

### G. Backward tracing from a click

The interaction to build for the demo: the user clicks a flagged spot on the map, picks a date, and the map highlights the upstream network within the chosen travel time, coloured by candidate score, with the rain history and satellite before/after in a side panel. A time slider then plays the forward simulation from a chosen candidate to show whether the pulse would have reached the anomaly at the observed time. This is the "wow, I understand it" moment the project brief asks for.

## 4. How the ideas rank

| Idea | Feasibility in a weekend | Visual impact | Honest evidence value | Verdict |
|---|---|---|---|---|
| A. Trigger-based EO look | High | High | Medium | Core |
| B. Digital twin lite | Medium to high | High | Medium | Core |
| C. Source-area scoring | High | Medium | Medium | Core |
| D. Sampling prioritisation | High | Medium | High | Frame the pitch around it |
| E. Virtual sensors | Medium | Low | High if validated | Stretch, one slide of results |
| F. Runoff-risk layers | Medium | Medium | Low to medium | Optional layer |
| G. Click-to-trace interaction | Medium | Very high | n/a (it is the interface) | Build first |

## 5. Recommended prototype

Working name: something like "TraceBack" or "Upstream". Pitch line: when the water board gets a signal, the map shows where to look first and why.

The system is a pipeline you can describe in one breath. Ingest rain, Sentinel-2 scenes, sampling points, the watercourse network and candidate discharge points. Compute a per-water-body anomaly for each satellite scene against its own seasonal baseline. Detect a trigger, either automatic (rain plus anomaly) or manual (a citizen report or a clicked point). Trace upstream through the graph within a travel-time window. Score candidates. Show the result on an interactive map with a time slider and a side panel of evidence.

Technical stack for speed: Python with rasterio, geopandas and networkx for processing; Sentinel Hub or openEO on CDSE for imagery; a static web map with MapLibre or Leaflet reading pre-computed GeoJSON so the demo cannot fail live. Precompute two or three example events and make them repeatable.

### Prep plan for this week

1. Open the Rijnland ArcGIS gallery and list the layers. Identify the watercourse network, structures and any discharge or overflow points.
2. Send the challenge owner a short question list (below) before the event.
3. Pick a study area of a few square kilometres with wide water and known structures, so the satellite has a chance.
4. Pull a year of Sentinel-2 L2A for that area, build a cloud-free time series, and compute the baseline per water body.
5. Build the network graph and test upstream and downstream tracing on it.
6. Draft the pitch in the order the project brief gives: problem, data gap, idea, how it works, prototype, example result, value for the water authority, limitations, next steps.

### Questions for the challenge owner

Which dataset would they consider the ground truth for a past incident? Do they have a known historical event, ideally with a lab result, that we can use to test whether the anomaly approach would have caught it? Where are the permitted discharge points and overflows, and can we have them as a layer? Is there any continuous sensor data (conductivity, temperature, oxygen), even from a few sites? What flow information exists for pumping stations and weirs? Can we access citizen reports with timestamps and locations? Which stakeholders would use this (enforcement, operations, ecology), and what decision would they make from it?

## 6. Limitations to state openly

A satellite anomaly is a possible signal, not a confirmed discharge. Ditches are narrower than a pixel, so the approach is strongest on wide water. Cloud cover and revisit time mean some events will have no usable scene. Empirical band-ratio formulas were calibrated elsewhere and need local validation. Travel-time estimates are rough without measured flows. Duckweed, shadows and bank pixels cause false positives, so water masking and vegetation filtering are part of the work. Any conclusion about a source needs field sampling to confirm.

## 7. After the hackathon

If the concept holds up, the extensions are calibration against Rijnland's full sampling record, higher-resolution imagery for narrow waters, a proper hydraulic model behind the graph, live sensor assimilation in the spirit of Pipedream-WQ, and a routine that sends the sampling team a daily priority list.

## Sources

- [Space & Water Nexus challenge page](https://connect.groundstation.space/space-water-nexus-hackathon-challenge-by-water-natuurlijk)
- [Satellietdataportaal](https://www.satellietdataportaal.nl/)
- [Rijnland: how water quality is measured](https://www.rijnland.net/over-rijnland/wat-doet-rijnland/schoon-water/monitoren-van-de-waterkwaliteit/)
- [Rijnland: Databureau Waterketen](https://www.rijnland.nl/over-rijnland/wat-doet-rijnland/afvalwater/digitalisering-van-de-waterketen/)
- [Copernicus Data Space: Sentinel-2 documentation](https://documentation.dataspace.copernicus.eu/Data/SentinelMissions/Sentinel2.html)
- [Se2WaQ Sentinel-2 water quality script](https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/se2waq/)
- [The potential of Sentinel-2 for water quality monitoring and reporting (Zenodo)](https://zenodo.org/records/18874938)
- [Sentinel-2 for chlorophyll-a water quality monitoring: a review (2026)](https://www.tandfonline.com/doi/full/10.1080/01431161.2026.2637851)
- [Monitoring coastal water turbidity using Sentinel-2, Los Angeles](https://www.mdpi.com/2072-4292/17/2/201)
- [Digital twin model for contaminant fate and transport in drainage networks](https://www.sciencedirect.com/science/article/pii/S1364815223002542)
- [Water quality management in the Netherlands (EARSC)](https://earsc.org/sebs/water-quality-management-in-the-netherlands/)
- [NLSA: European satellite data and Dutch waters](https://www.nlsa.nl/en/news/european-satellite-data-offer-ample-opportunities-for-the-dutch-coast-and-waters/)
