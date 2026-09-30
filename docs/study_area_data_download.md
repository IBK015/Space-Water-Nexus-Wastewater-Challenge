# Study area data download and clip: Alphen aan den Rijn and Bodegraven-Reeuwijk

Everything the challenge needs a spatial extent for is now downloaded, clipped and sitting in your Nexus Hackathon folder on your own machine. QGIS on your computer did the actual downloading: its Python has real internet access, unlike the cloud sandbox and the local shell tried earlier in this project, both of which are blocked from reaching Rijnland's server.

## What defines the study area

Rijnland's two-municipality pilot area, Alphen aan den Rijn and Bodegraven-Reeuwijk, pulled from PDOK's official gemeente boundaries and buffered by 500 metres so the network isn't cut off right at the admin line. In Dutch RD coordinates (EPSG:28992) that buffered box is roughly x 95,749 to 120,255, y 446,891 to 465,106, about 24.5 by 18.2 kilometres.

## What got downloaded

Three sources, all queried directly against the study area so nothing board-wide or nationwide had to be dragged in:

Rijnland's ArcGIS map server: 29 layers from the priority list, everything from watercourse centrelines and polygons to discharge points, overflow structures, treatment plants, pumping stations, sewer areas, level areas and flood defences. Saved as GeoJSON under `01_data/raw/rijnland_mapserver/`, organised by theme exactly as the folder README describes.

The 2019 sewer pilot dataset (`Pilot_data_portaal_afvalwaterketen`): 40 sub-layers covering Alphen aan den Rijn and Bodegraven-Reeuwijk specifically, pumps, manholes, pressure and gravity pipes, overflow points, pump catchments and drainage surfaces. This is the one place Rijnland's own data gives real sewer network connectivity rather than just point locations, so it's worth the extra sub-layers. Saved under `01_data/raw/rijnland_mapserver/03_wastewater/pilot_2019/`.

PDOK's Richtlijn Stedelijk Afvalwater (RSA): the national, independently reported register of treatment plants, discharge points, agglomerations and sensitive receiving areas. Saved under `01_data/raw/pdok_rsa/`. This is the cross-check source flagged in the open-data note sent earlier today.

Worth flagging: a few of the busiest 2019 pilot layers (the Alphen manholes and pipe network) first came back truncated by a safety cap in the download script, at roughly a quarter of their real count. That got caught by comparing against the server's own count endpoint, then re-downloaded in full, so the numbers below are the corrected ones.

## What's in the clipped GeoPackage

`01_data/processed/study_area.gpkg` holds all 69 layers, precisely clipped to the buffered study area polygon (not just a bounding box), in EPSG:28992, ready to open in QGIS. 377,331 features in total. The heaviest layers are the watercourse network itself, Watergang_as at 39,443 features and Watergang_vlak at 31,357, which reflects how dense the ditch network genuinely is across two full polder municipalities, not a data error.

A companion file, `01_data/processed/study_area_boundary.gpkg`, holds just the dissolved municipality outline, and `study_area_buffer500m.gpkg` holds the buffered version used for all the clipping.

`01_data/raw/MANIFEST_study_area.csv` lists every layer in the GeoPackage with its feature count, geometry type and source, so anyone on the team can see what's there without opening QGIS.

## The study area map

A print-ready overview map is attached, and also saved as a QGIS project at `04_prototype/study_area_map.qgz` (plus the rendered PNG in the same folder) so it can be reopened and adjusted. It shows the two-municipality boundary, the watercourse network, and every wastewater-relevant point layer: treatment plants, discharge points, overflow structures and the 2019 pilot sewer overflows, with the national PDOK RSA plants and discharge points layered in for the cross-check story.

One implementation note in case this comes up again: QGIS's print layout tool silently dropped most layers when exporting through its normal legend-and-scalebar composer, for reasons that didn't trace back to anything wrong with the data or the styling. The plain map canvas render worked correctly, so the title, legend, north arrow and scale bar on the attached map were composited on afterward instead. Worth knowing if you build further layouts in this same QGIS project.

## Reading the map honestly

The orange triangles (2019 pilot sewer overflows) cluster tightly in the two town centres, which is exactly what combined-sewer overflow geography should look like, not a sign of a data problem. The yellow diamonds (the board-wide Overstortconstructie register) spread more widely, including areas the 2019 pilot doesn't cover, which is useful since it means overflow candidates exist for the demo outside just the two pilot towns. The red circles (Lozingspunt) are genuinely sparse, 27 points across the whole study area, which matches what the earlier data review already found: it's a real but thin registry, not the richer source the original proposal PDF assumed.

## What this changes for the build

The team now has, on this machine, everything needed to build the candidate-source scoring feature discussed earlier: discharge points and overflow structures with their attributes, a real (if 2019-vintage) sewer network graph for Alphen and Bodegraven-Reeuwijk, and the watercourse network with width attributes for the satellite-observability mask. Nothing here still depends on the cloud sandbox's network access, since the whole download ran through QGIS on your own computer.

## Still open

Whether the discharge volume (DEBIET) and frequency fields are actually filled in across these 27 Lozingspunt records, worth checking directly in QGIS's attribute table. Whether the 2019 pilot data is close enough to today's network to trust for a live demo, six years is a meaningful gap for sewer infrastructure. And the same open question as before: no source found yet gives an actual log of when an overflow spilled, only structural and permitted-capacity data.
