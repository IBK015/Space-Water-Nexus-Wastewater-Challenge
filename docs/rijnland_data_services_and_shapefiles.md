# Rijnland spatial data: services behind the maps, and how to get shapefiles

Note: the full service list (96 services, including Lozingspunt, Overstortconstructie, Effluentgemaal and IBA) is in rijnland_16_tiles_and_downloads.md. This note covered only part of it.

Follow-up to the gallery summary. The map canvases in the gallery did not draw their basemaps in my browser, so instead of reading pixels I went to the source: Rijnland runs a public ArcGIS Server whose services feed those maps. I read the service pages and layer definitions (read only). I have not downloaded any data.

## Is there a shapefile?

Not as a ready-made download that I could find. The Rijnland pages for the legger (surface water and structures) point only to the interactive map and say nothing about downloads, and the "open datasets" page on the site is about archive collections, not GIS data. What does exist is better for a hackathon: a public REST services directory at `rijnland.enl-mcs.nl/arcgis/rest/services` with query support in JSON, GeoJSON and PBF, in Dutch RD coordinates (EPSG:28992). The layers I checked also advertise WMS/WFS. GeoJSON can be turned into a shapefile or GeoPackage in one command, or the services can be added to QGIS directly. Each layer limits a query to 1,000 or 2,000 records, so larger layers need paging.

Two alternatives to check: the INSPIRE hydrography dataset for the waterschappen listed on data.overheid.nl (the page returned an error when I opened it, so I could not confirm whether Rijnland is included or which formats it offers), and the national base registers on PDOK (verify). The service directory is the most direct route.

## What the services directory holds

The root has ten folders (Deregulering, EFM2020, EFM2021, Gebied, KPI2020, Leggers, Schouw, Utilities, WBP6, Werkingsgebieden) plus loose services. The ones that matter for us:

| Service | What it is |
|---|---|
| Leggers/Legger_Oppervlaktewater_Vigerend | Five layers: Watergang begroeiing, Watergang As (centreline), Watergang Vlak (polygon), Kernzone, Beschermingszone. Extent about X 83,000 to 119,000, Y 446,000 to 497,000. |
| Watergang_as, Watergang_vlak (also FeatureServer), Watergang_zone | The watercourse layers as separate services |
| Meetlocatie_waterkwantiteit | Measurement locations for water quantity |
| Meetlocatie_waterkwaltiteit (sic, spelled this way) | Measurement locations for water quality |
| TransportleidingMeetpunt | Measurement points on the transport pipeline |
| Gemaal, Gemaal_opgrootte, Stuw, Duiker, Duiker_punt, Inlaat, Sluis, Dam, Brug, Aquaduct, Vispassage | Structures |
| Afvalwaterzuivering, Zuiveringseenheid | Treatment plants and treatment units |
| Zwemwaterlocatie, Zwemwaterlocatie_badzone | Bathing water locations and zones |
| Pilot_data_portaal_afvalwaterketen | Sewer data for three municipalities, 2019 (see below) |
| Leggers/ (folder) | Also structures (Legger_kunstwerkenVigerend), regional and primary flood defences, and consultation ("inspraak") versions |

## Layers worth knowing in detail

**Watergang As** (polyline) has BREEDTE (width), LENGTE, WATERDIEPTE, BODEMBREEDTE, bank slopes, category of surface water body, DRAINEERT, maintenance fields, and survey metadata including accuracy and date. This confirms the width attribute for the observability mask. It has no flow direction field in the list I read, so direction has to come from structures and level areas.

**Meetlocatie (waterkwantiteit)** is the richest layer for the twin. It has CODE, TYPEMETING, LIZARDID, a measurement interval and a reception interval (MEETINTERVAL, ONTVANGSTINTERVAL), and link fields to the object it measures (KOPPELINGOBJECT, NAAMOBJECT, SOORTOBJECT, capacity, weir width and sill height). It also carries the management hierarchy: level area (peilgebied) with summer and winter targets, polder, boezem area, drainage (afwateringsgebied) area, and a discharge-function flag (DEBIETFUNCTIE). The LIZARDID field suggests the time series live in a Lizard platform (a Nelen & Schuurmans product). That is my inference from the field name, so ask whether a Lizard API is open to us.

**Meetlocatie (waterkwaliteit)** has CODE, NAAM, sample layer (MONSTERLAAG), a description of the measurement, a hyperlink, KRW water type, and last-edited date. The layer descriptions in the service are worded as if for water quantity, which looks like a copy-and-paste in the metadata. Check what it really contains once loaded.

**Pilot_data_portaal_afvalwaterketen** is the surprise. It holds 2019 sewer data for Alphen aan den Rijn, Bodegraven-Reeuwijk and Waddinxveen: pumps, manholes, structures, gravity and pressure conduits, drainage surfaces (roofs, paved and open surfaces, for both stormwater and mixed systems), pump areas, and overflow structures. The Alphen overflow layer has from-node and to-node numbers, sill number, height, width, a discharge coefficient and a flow direction code. That is the connectivity we would need to trace from an overflow to a receiving water, for those three municipalities only, and only as of 2019.

## What this means for the plan

A study area is now easy to justify. The three pilot municipalities have sewers, overflows, pump areas and drainage surfaces in one service, next to the watercourse network and structures. Choosing an area around Alphen aan den Rijn and Bodegraven-Reeuwijk would give a network with overflows (candidate discharge points), pump areas (candidate trigger zones) and watercourses with widths (satellite-observable or not). That would let the demo show the full chain, not only a channel graph.

The level areas, polders and boezem codes on the measurement points give the polder-and-boezem structure directly, so I would not need to infer it from the map.

Two things stay unknown. First, whether the measurement layers hold values or only locations: the fields I read are metadata, so the readings themselves probably come from another source. Second, whether these services are licensed for reuse, since Rijnland's dashboards carry a no-copying notice. Ask the challenge owner.

## Suggested next step

With your OK, I can pull a small test extract (for example the watercourse centrelines, structures and measurement points around Alphen aan den Rijn) as GeoJSON and convert it to a shapefile or GeoPackage. I would do it in the cloud workspace and save it in your Nexus Hackathon folder, or load the services straight into QGIS on your computer, which needs no download. Tell me which you prefer and roughly which area.

## Sources

- [Rijnland ArcGIS REST services directory](https://rijnland.enl-mcs.nl/arcgis/rest/services)
- [Legger_Oppervlaktewater_Vigerend MapServer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Leggers/Legger_Oppervlaktewater_Vigerend/MapServer)
- [Meetlocatie_waterkwantiteit layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Meetlocatie_waterkwantiteit/MapServer/0)
- [Meetlocatie_waterkwaltiteit layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Meetlocatie_waterkwaltiteit/MapServer/0)
- [Pilot_data_portaal_afvalwaterketen MapServer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Pilot_data_portaal_afvalwaterketen/MapServer)
- [Rijnland: legger oppervlaktewateren](https://www.rijnland.nl/zakelijk/legger/legger-oppervlaktewateren/)
- [Rijnland: legger ondersteunende kunstwerken](https://www.rijnland.nl/zakelijk/legger/legger-ondersteunende-kunstwerken/)
