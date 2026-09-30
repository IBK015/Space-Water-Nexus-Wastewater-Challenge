# Space & Water Nexus – Wastewater Challenge

Prototype and research repo for the **Space & Water Nexus Hackathon 2026**, built around the Water Natuurlijk Rijnland challenge: *"Granular insights into wastewater discharges."*

Challenge brief: [`docs/waste_water_challenge.md`](docs/waste_water_challenge.md)
Event info: https://connect.groundstation.space/space-and-water-nexus-hackathon-1-3-october-cometlab

## The problem

Rijnland already collects water-flow and water-quality data, but it's too coarse to pin down wastewater discharges at their source. That makes it hard to say what happened, when, and where, which limits the authority's ability to investigate illegal or over-permit discharges. This project explores whether combining that existing monitoring data with (near) real-time satellite observations can close some of that gap, or at least narrow down when and where something worth investigating may have happened.

## Repository structure

```
data/
  raw/         Downloaded source layers from Rijnland's Legger services and PDOK, organised by theme
  processed/   Derived study-area boundaries and clipped datasets
  reference/   Layer-priority notes and other reference tables
docs/          Research notes, data-source reviews, and dashboard/concept write-ups from the project
maps/          Rendered study-area map iterations (PNG)
```

### About the compressed files

A handful of the larger vector layers (anything that was going to land over ~20 MB as plain GeoJSON, plus the combined study-area GeoPackage) are stored gzip-compressed (`.gz`) to keep the repo a reasonable size. To use one:

```bash
gunzip -k path/to/file.geojson.gz
```

Or read the gzip directly without decompressing first, which works fine with GDAL/geopandas:

```python
import geopandas as gpd
gdf = gpd.read_file("data/raw/rijnland_mapserver/01_watercourses/Watergang_vlak.geojson.gz")
```

`data/raw/MANIFEST_study_area.csv` and `data/raw/rijnland_mapserver/MANIFEST.csv` list every downloaded layer with its source service, feature count, and geometry type.

## Data sources

- **Rijnland Legger services** (`Legger_Oppervlaktewater_Vigerend`, wastewater/sewer layers, monitoring locations, water levels, flood defences) — the authority's statutory GIS registers, pulled via their ArcGIS MapServer.
- **PDOK** — national reference layers (provinces, municipalities) and the RSA (Rioolwaterzuivering/sewage treatment) dataset: agglomerations, vulnerable areas, discharge points, WWTPs.
- See `docs/rijnland_data_services_and_shapefiles.md`, `docs/rijnland_16_tiles_and_downloads.md`, and `docs/rijnland_gallery_summary.md` for how each service was found and what it contains.

## Study area

The study area and its context layers were clipped from the wider Rijnland dataset; see `docs/study_area_data_download.md` for the selection method and `data/processed/` for the resulting boundary and buffer files.

## Concept and dashboard ideas

`docs/digital_twin_dashboard_concepts.md` and `docs/deep_research_digital_twin_dashboard.md` cover the prototype directions considered for the hackathon demo: an anomaly map paired with a flow-based source-tracing view, built around satellite observations flagging candidate areas of interest that still need ground-truthing rather than confirmed pollution events.

## Maps

`maps/` holds the successive iterations of the study-area overview map, ending with `study_area_map_full_layout.png`, which combines a regional locator, an infrastructure close-up, and a full legend.

## Important caveat

Nothing here claims that a satellite signal proves a specific discharge or pollutant source. Satellite data can help flag anomalies and narrow down areas, timing, and possible sources; confirming an actual discharge still needs ground measurements. Results are described as potential anomalies or candidate areas, not confirmed pollution.
