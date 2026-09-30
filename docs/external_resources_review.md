# External resources review: GeoLibre, DINOv2, Waterdata doc, mentors guide, and the trigger-forecasting proposal

Four things came in during the same stretch of work: the GeoLibre website, a DINOv2 remote-sensing repository, a shared "Waterdata" document on OneDrive, and two files sitting in `01_data/Other` on the device, a proposal PDF and a mentors guide slide deck. Here's what's in each, and what to do with it.

## GeoLibre (geolibre.app)

GeoLibre is a free, open-source browser-based GIS built on MapLibre GL, DuckDB-WASM Spatial, and deck.gl. Everything runs client-side: no server account, no install, and (this is the part that matters for us) it can point directly at a WMS, WFS, ArcGIS REST endpoint, or STAC catalog and pull the data in live. That's relevant to our situation specifically: our cloud sandbox and the linked device are both blocked from reaching `rijnland.enl-mcs.nl`, but the user's own Chrome isn't. Loading Rijnland's ArcGIS layers straight into GeoLibre Web, by URL, would sidestep the download problem entirely for exploration purposes, even if we still want the data downloaded and versioned for the actual submission.

A few features line up well with the prototype:

- A Spectral Index toolbox with NDVI/NDWI/EVI band presets, useful if we get Sentinel-2 tiles into it directly.
- A Time Slider plugin, which is close to the "pick a date, see what changed" interaction the challenge brief asks for.
- 1,000+ Whitebox processing tools (buffer, clip, zonal statistics, raster calculator, and so on) running in WebAssembly, no Python sidecar needed.
- A story map builder with standalone HTML export, which could work as the narrative layer for the pitch itself.
- An AI Assistant that turns plain English into spatial SQL or map operations, a nice demo touch but not core to the detection logic.

It's a general-purpose GIS tool, not something built for water-quality remote sensing, so the actual anomaly scoring and trigger logic still need custom code either way. GeoLibre would be the map and data layer, not the analysis engine. Worth a short spike, maybe half an hour, loading two or three Rijnland layers by URL to check rendering and performance before deciding whether it replaces the Streamlit/Leaflet stack the proposal below suggests.

## dinov2-remote-sensing (github.com/chagmgang/dinov2-remote-sensing)

This is a PyTorch implementation of DINOv2 (self-supervised vision transformers) trained on about 6 million generic remote-sensing scene images, mostly Million-AID and SkyScript. It's evaluated on scene classification benchmarks like EuroSAT, RESISC, and UC Merced, in other words, "what kind of land cover is this tile," not turbidity, chlorophyll, or anything water-quality specific. Pretrained ViT-S/B/L checkpoints are on Hugging Face, and the intended uses are linear-probe classification, k-NN, and image retrieval via feature embeddings.

The angle that could apply to us: embedding distance from a rolling baseline is, in principle, another anomaly detector, conceptually similar to the z-score approach the proposal below already suggests. But the model wasn't trained on Sentinel-2 water surfaces, needs its own preprocessing to match training conventions, and a full setup (even inference-only) is more of a two-day side project than something to build into a three-day hackathon MVP. A simple spectral-index anomaly score (NDWI or a turbidity band ratio, compared against a historical baseline) will be faster to build, easier to explain to non-technical judges, and doesn't need a GPU. If there's spare time near the end, a small "here's a direction we didn't have time to pursue" slide showing embedding distance over a handful of Sentinel-2 chips could be a reasonable next-steps talking point, but it shouldn't be load-bearing for the demo.

## Waterdata.docx (OneDrive, "anyone can edit")

This one only came through partially. The link resolves to a Word Online document called "Waterdata," about 450 words over what looked like two or three pages. The Chrome tab kept freezing on scroll and on ribbon clicks (the same rendering issue we've hit before on heavy web apps), so only the first section was readable:

- **Water:** water level relative to NAP, water drainage, water temperature, flow velocity (with direction), wave height, tide, salt content
- **Wind and air:** wind force (with direction), air temperature
- **Water quality:** links out to Eijsden (Maas) and Lobith (Rhine) monitoring stations

All of this is Rijkswaterstaat data, the national authority for major rivers, canals, and coastal waters, not Rijnland's regional network. It's a different scale of water body than the polders and ditches we're focused on, but Rijnland's boezem does discharge into Rijkswaterstaat-managed water (the Noordzeekanaal, the Hollandsche IJssel, the North Sea), so this could serve as a downstream boundary condition or a sanity check on any propagation claims, rather than a core data source. Since it's an editable, shared document, it may be a running list other teams or organizers are adding to. I couldn't get past the first section because the tab stopped responding. Worth either reopening it fresh, asking whoever shared it to paste the rest, or trying again later.

## Hackathon Mentors Guide (found alongside the proposal, in `01_data/Other`)

Not something that was asked for directly, but it was sitting next to the proposal PDF and it answers a question we've flagged more than once: who to actually ask about the things we don't know yet. Two people are the named contacts for the wastewater challenge (C3):

- **Marc Minnee** (Water Natuurlijk, committee member at Rijnland Water Board), on site Thursday, Friday, and Saturday. His listed focus is exactly ours: water quality, water-authority processes, and combining existing water data with satellite data.
- **Kees Buskermolen** (Rijnland Water Board assembly), on site Saturday 9:00 to 15:00 only. His focus covers pollution dispersal through canals and satellite water-level measurement.

Both are worth the open questions already sitting in our research notes (measurement update intervals, API access to the Hydronet-sourced Actuele Metingen feed, whether any labelled historical pollution events exist, licensing terms for the map-server data). Kees is only there for a few hours on the final day, so anything that needs his specific angle (pollution dispersal, satellite water levels) should be asked early Saturday rather than left for later. Two other mentors are useful for any team regardless of challenge: Martijn Seijger and Andrei Bocin-Dumitriu (dotSPACE Foundation) cover business development, EO use cases, and turning an idea into a working prototype.

## Reviewing the proposal (Rijnland_ML_Trigger_Forecasting_Wastewater_Proposal.pdf)

Someone (not clear who, the file just appeared in the folder) wrote up a 12-page concept proposal: "ML-Based Trigger Forecasting for Near-Real-Time Wastewater Pollution Detection and Source Tracing," dated September 2026. It's a solid piece of work, better organized than most hackathon concept documents, and worth building on rather than starting over. Here's where it holds up and where it doesn't.

**What's genuinely good:**

The trigger-based framing is the right call: instead of continuously classifying every reading as polluted or not, the system learns normal conditions and only raises an alert when the evidence crosses a calibrated threshold. That matches how a water authority would actually want to consume this, as alerts, not as a raw score stream. The pipeline (observe, predict, trigger, trace, forecast, visualise) is clean and demoable.

The document is careful about what it's actually claiming. It repeats, more than once, that a trigger is a decision-support signal, not proof of an illegal discharge or a confirmed source, and it uses language like "candidate source area" and "investigation priority" rather than overclaiming. That's exactly the caution our own project brief asks for, and it's rare to see a hackathon proposal hold that line consistently.

The validation section is the strongest part of the whole document. It explicitly separates historical-event confidence into tiers (confirmed discharge versus public report versus threshold exceedance alone), insists on time-aware train/test splitting with a clear diagram of what counts as data leakage, and defines three separate metric groups: detection metrics, operational trigger metrics, and source-tracing metrics. That level of rigor is more than most three-day builds bother with.

The tech stack (Python, pandas, GeoPandas, scikit-learn or XGBoost, Google Earth Engine plus Sentinel-2, NetworkX and PostGIS for the network side, Streamlit or Dash with Leaflet or MapLibre) is realistic and buildable in the time available, and it lines up closely with what we'd already scoped ourselves.

**Where it's thin, and where our own research already has better material to plug in:**

The proposal treats "known pollution events" as a data category it can just plug into a supervised classifier, but from everything we found on Rijnland's ArcGIS server, there is no confirmed layer of labelled, dated, located pollution incidents, only structural and metadata layers like Lozingspunt and Overstortconstructie. If Rijnland can't hand over even a small set of confirmed incidents, the supervised-learning branch (section 6.1) has nothing to train on, and the anomaly-detection branch (6.2), which the document frames as a secondary method, is probably where the actual MVP has to start. This is worth resolving with Marc Minnee or Kees Buskermolen before the team commits build hours to the supervised path.

Section 8, the satellite data integration section, is the thinnest technical part of the document: half a page of general Sentinel-2 facts, no specific choice of index (NDWI versus a turbidity-specific band ratio versus the more tailored formulas we'd already scoped), and no mention of the pixel-size and channel-width limits that matter a lot for Rijnland's narrow ditches. It also doesn't use the width attribute (BREEDTE) on the Watergang_As layer, which is exactly what would let us build an observability mask, marking which watercourses are even wide enough for Sentinel-2 to say anything about. Rewriting this section with our own findings would close the gap between "we mention Sentinel-2" and an actual working spatial layer in the demo.

The document never touches the real Rijnland network we've already mapped. Section 10's upstream-tracing example (station A to B to C to D) is a generic illustration, not tied to an actual watercourse or pump zone. The 2019 Pilot_data_portaal_afvalwaterketen layer, which has real from-node and to-node connectivity for the Alphen aan den Rijn and Bodegraven-Reeuwijk pilot area, is the closest thing we've found to real network topology, and the proposal doesn't cite it at all.

The threshold-calibration section is a little circular without labelled events: "calibrate against historical events" doesn't help if there aren't enough of them. The dynamic-threshold formula it proposes (an anomaly score built from observed versus expected value, scaled by expected variability) is sound and could end up doing most of the actual detection work even without a trained classifier, which is worth being upfront about rather than leaning on "ML-based" in the framing when the trained model might not be trustworthy with only a handful of labelled examples by October.

The EC and water-temperature readings from Rijnland's own Actuele Metingen feed, the closest thing Rijnland already has to a live wastewater tell, aren't mentioned specifically, even though the proposal's generic variable list does include conductivity and temperature. Worth naming that feed directly, and noting that we still don't have confirmed programmatic access to it (that's a real blocker to flag early, not just a data source to list).

The dashboard mockup is a reasonable static screen but doesn't commit to an interaction model. Given the challenge brief's "pick a date or location and see it happen" requirement, the demo needs at least one clickable interaction, and right now the document just describes what the final product "could" show.

**Bottom line:** it's a strong concept document, stronger on validation rigor and honest framing than most hackathon submissions, but generic in exactly the two places where two days of our own research already have concrete, Rijnland-specific material ready to drop in: the satellite index choice and the real discharge and overflow network. The single riskiest assumption in the whole plan is that labelled historical pollution events exist somewhere; that's the first question to put to Marc Minnee or Kees Buskermolen, since the rest of the architecture (supervised model versus anomaly-only) depends on the answer.
