# Nexus Hackathon 2026: wastewater discharges (Water Natuurlijk Rijnland)

Challenge: use near-real-time satellite data together with existing water flow and water quality data to find out when and where possible pollution occurred and which areas could be the source. Event: Space & Water Nexus Hackathon, 1 to 3 October 2026.

## Status

The folder structure, notes, layer catalogue and download script are ready. **The data itself has not been downloaded yet.** The cloud workspace and the shell on this computer both blocked the Rijnland server (`rijnland.enl-mcs.nl`) through their network policy, so no files could be fetched from there. Run the download from a machine that can reach the server (steps below). The script was tested end to end against a local mock of the server, not against the real one, so watch the first run.

## Folder map

```
Nexus Hackathon/
├── README.md                     this file
├── run_download.bat              Windows: double-click to download everything
├── run_download.sh               Mac/Linux equivalent
├── 00_docs/
│   ├── challenge/                the challenge brief
│   ├── research/                 research notes and concept ideas
│   └── rijnland_data/            what the Rijnland portal offers, and how to get it
├── 01_data/
│   ├── raw/                      untouched downloads, one subfolder per source (never edit by hand)
│   │   └── rijnland_mapserver/   GeoJSON per layer, sorted by theme, plus MANIFEST.csv and a log
│   ├── processed/                GeoPackages and shapefiles built from raw (EPSG:28992)
│   ├── external/                 data from other sources (satellite scenes, rainfall, land use)
│   └── reference/                rijnland_layer_priority.csv: which layers matter and why
├── 02_scripts/                   download_rijnland.py
├── 03_analysis/                  notebooks and analysis scripts
├── 04_prototype/                 the map / dashboard / simulator
└── 05_pitch/                     slides, figures, demo screenshots
```

Numbered folders sort in workflow order. Raw data is never edited: anything derived goes in `processed/` or `03_analysis/`.

## Download the data

You need Python 3.9 or newer. There are no extra packages to install. From this folder:

1. Preview first: `python 02_scripts/download_rijnland.py --list` shows every layer and its feature count without downloading features.
2. Then download: double-click `run_download.bat` (Windows) or run `./run_download.sh`. This downloads all layers except a short skip list (a cadastral parcels layer, an archive collection, a sample layer) and then builds GeoPackages and shapefiles if GDAL is present (it is bundled with QGIS).
3. Check `01_data/raw/rijnland_mapserver/MANIFEST.csv`. Every layer has a status (ok, empty, partial, error), the expected and downloaded feature counts, the source URL and a checksum.

Useful options:

| Goal | Option |
|---|---|
| Only the wastewater layers | `--themes 03_wastewater` |
| Only some services | `--services "lozingspunt,overstort,gemaal"` |
| Only a study area | `--bbox-rd xmin,ymin,xmax,ymax` in EPSG:28992 (get the extent from QGIS) |
| Resume after an interruption | run the same command again, finished layers are kept |
| Re-download everything | `--force` |
| Include the skipped heavy layers | `--include-heavy` |

The script asks for at most 500 features at a time and waits 0.2 seconds between requests, so it stays polite to the server.

## What you get

| Theme folder | Contents |
|---|---|
| 01_watercourses | Watercourse centrelines and polygons, protection zones, natural banks |
| 02_structures | Pumping stations, weirs, culverts, inlets, sluices, bridges, siphons |
| 03_wastewater | Discharge points, overflow structures, effluent pumps, small treatment systems, plants, sewer areas and flow directions, pipelines, 2019 pilot sewer data |
| 04_monitoring | Measurement locations (quantity and quality), bathing water sites, water bodies |
| 05_levels_and_areas | Level areas, polders, boezem, drainage units, municipalities |
| 06_flood_defences | Regional and primary flood defences (context only) |
| 07_projects_and_other | Dredging projects and similar |
| 99_other | Anything the script could not classify |

Raw files are GeoJSON in EPSG:4326, exactly as the server returns them, each with a `.meta.json` next to it (source URL, field names and types, description). Processed files are in EPSG:28992, the Dutch RD New system. Shapefile field names are cut to 10 characters by the format, so the GeoPackages, which keep full names, are the better working copy.

## Not in the download

The live station readings (rain, water level, flow, pump status, chloride, conductivity, temperature) are not on this server as far as I could see. The Actuele Metingen page says they come from Hydronet. Ask the challenge owner how to get the time series. The Power BI dashboards (water quality, agricultural network, Water Framework Directive status, fish) are not covered either. See `00_docs/rijnland_data/01_rijnland_16_tiles_and_downloads.md`.

## Terms of use

Rijnland's pages state that copying, sharing or reproducing their material without permission from the makers is not allowed. The terms for the map server data are not stated where I looked. Treat the data as for internal hackathon use until the challenge owner confirms the terms, and do not publish it or put screenshots of their dashboards into the pitch without asking.

## Suggested first study area

Alphen aan den Rijn and Bodegraven-Reeuwijk. The 2019 pilot sewer data covers them, three treatment plants are nearby, and there are large lakes where satellite data can say something. Confirm the extent in QGIS and pass it to `--bbox-rd`.

## Where to start reading

1. `00_docs/rijnland_data/01_rijnland_16_tiles_and_downloads.md` for the data landscape
2. `00_docs/research/02_digital_twin_dashboard_concepts.md` for the prototype ideas
3. `00_docs/research/01_research_and_brainstorm.md` for the satellite and method background
4. `01_data/reference/rijnland_layer_priority.csv` for the layer shortlist

The two files ending in `_superseded` in `00_docs/rijnland_data/` are kept for history and contain two statements the newer note corrects.
