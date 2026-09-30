# Rijnland "op de kaart": the 16 tiles, what data can be downloaded, and how it ties to the wastewater challenge

This replaces my two earlier notes on the gallery where they disagree. Two corrections up front. First, I said no layer for outfalls or sewer overflows was visible. That was wrong: the map server behind the gallery has both (see section 3). Second, Rijnland's own description says the Actuele Metingen readings come from Hydronet, a monitoring supplier, so ask for the data through Rijnland, not through the map.

The maps rendered properly this time, and I also read the gallery's group listing, which gives Rijnland's own one-line description of each tile. I have not downloaded any data.

## 1. The gallery in one paragraph

"Rijnland op de kaart" is the water board's public map portal. It holds 16 items: registers of what exists (watercourses, structures, flood defences), planning and decision layers (water levels, dredging, the six-year water programme), monitoring dashboards (current measurements, water quality, agricultural network, fish), the wastewater chain, and two access guides (web services and open data). For our challenge, the useful ones are the wastewater chain (11), current measurements (06), the watercourse and structure registers (03, 04), the water level areas (08), the water body maps (09, 10) and the two access tiles (15, 16). The rest is context or false-positive filters.

## 2. Tile by tile

| # | Tile | What it shows | Link to our challenge |
|---|---|---|---|
| 01 | Legger regionale keringen | Regional flood defences: defence axis, boezem embankments, polder embankments, Rijnland boundary. | Low. Shows the boezem and polder structure of the region. |
| 02 | Legger primaire waterkeringen | Primary flood defences, same style. | Low |
| 03 | Legger oppervlaktewater | The official register of all surface water: watercourse centrelines drawn as primary (dark blue) and other (light blue). Attributes include width, depth and bank slopes. | High. This is the channel network for the graph, and the width lets us decide where a 10 m satellite pixel can say anything. |
| 04 | Legger ondersteunende kunstwerken | Supporting structures on the same watercourse map: culverts, inlets, nature-friendly and valuable banks, plus the water polygons. | High. Structures set how water is connected and moved. |
| 05 | Baggeren project | Dredging by watercourse stretch, coloured by phase (initiation, preparation, implementation). The description adds a completed phase. Covers the whole board area, with heavy activity around Alphen aan den Rijn and Leiden. | Medium. Dredging stirs sediment, so it explains turbidity that has nothing to do with wastewater. A useful false-positive filter. |
| 06 | Actuele Metingen | Rain, boezem and polder water levels, pump flow, pump on/off status, chloride, electrical conductivity, water temperature. Measured by Hydronet for Rijnland. The description lists nine maps, the page showed eight tabs. | Very high. These are the live stations. |
| 07 | Waterkwaliteit (Power BI) | Tests of phosphorus, nitrogen, chloride, zinc and copper against Water Framework Directive norms, using summer averages. Water types run from fresh ditches to regional canals. I read only the overview page. | Medium to high as a chemical baseline |
| 08 | Peilbesluiten met peilafwijkingen | Level areas (peilgebieden) with their fixed summer and winter water level, plus areas allowed to deviate (dewatering or flood supply), with or without a permit. | Medium to high. The level areas are the sub-catchments the twin will be built from. |
| 09 | KRW waterlichamen | Water Framework Directive water bodies (boundaries from 2022), the larger lakes, canals and rivers. | High for the satellite side, since they are the waters big enough to see |
| 10 | KRW 2025 dashboard (Power BI) | Status, measures and trends per water body, updated once a year. Rijnland has 40 water bodies and the KRW covers waters above 50 hectares. | Medium. Shows how coarse the current reporting is. |
| 11 | Afvalwaterketen | Sixteen or more treatment plants named on the map (Haarlem-Waarderpolder, Zwanenburg, Zwaanshoek, Lisse, Noordwijk, Katwijk, Leimuiden, Nieuwe Wetering, Leiden Noord, Leiden Zuid-West, Nieuwveen, Alphen Kerk en Zanen, Alphen-Noord, Bodegraven, Waddinxveen-Randenburg, Gouda), sized by capacity, plus influent pumping stations (orange squares, my reading) and the transport pipelines to the plants, shown as realised or planned. Neighbouring plants at Houtrust, Harnaschpolder and Haastrecht appear too. | Very high for candidate discharge points and which plant serves which area |
| 12 | Agrarisch meetnet (Power BI) | Monthly pesticide and nutrient measurements at fixed sites in arable, bulb, tree nursery, greenhouse and dairy areas, since 2014. | Medium to high. Separates farm runoff from wastewater. |
| 13 | Waterbeheerprogramma WBP6 | Policy website for the 2022 to 2028 programme. | Low. Context for the pitch. |
| 14 | KRW visstand | Fish monitoring per water body. | Low |
| 15 | WebService Rijnland | A viewer holding every web service Rijnland offers. Rijnland's own description says users can view, select and download data from it. Groups include Afvalwater, Gebied extern, Gebied Rijnland, Keringen, Kunstwerken, Legger Rijnland, Peil en poldergebied, Schouw, Watergang Systemen, Zwemwater, and an 8 cm aerial photo layer. The Afvalwater group holds Afwateringseenheid, Afsluiter, Afvalwaterzuivering, Effluentgemaal, IBA and more. | Very high. The data catalogue. |
| 16 | Open Data voor GIS-professionals | A story collection with eight chapters explaining the open map data and, per its description, how to use the maps and download the data. I read only chapter 2 (attribute pop-ups). | High. Ask for or read the download chapters. |

## 3. The wastewater layers on the map server

The gallery is built on Rijnland's public ArcGIS map server (`rijnland.enl-mcs.nl/arcgis/rest/services`), which lists 96 services at its root. The ones that matter to us:

**Lozingspunt (discharge point).** Point layer with name, type of discharged water (SOORTGELOOSDWATER), discharge volume (DEBIET), frequency (FREQUENTIE), owner and manager, and links to the treatment plant (CODERWZI), the receiving watercourse (CODEWATERGANG), a small on-site treatment system (CODEIBA), a pipeline (CODELEIDING) and a sewer area (CODERIOLERINGSGEBIED). This is close to a ready-made "possible sources" list, already tied to the watercourse network.

**Overstortconstructie (overflow structure).** Point layer with type of overflow, sill height, overflow frequency, peak volumes at return periods of 1, 2, 5 and 10 years, connected surface area, direction, owner and construction year.

**Effluentgemaal (effluent pumping station).** Points with design, current and installed capacity, number of pumps, discharge direction and the treatment plant it belongs to.

**IBA (individual wastewater treatment).** Points for small local treatment systems, with type, type of discharge, address, status and permit exemption fields.

**Rioolstelsel, Rioolgemaal_influent, Transportleiding, Overnamepunt.** The sewer and pipeline network: pipe type, diameter, invert levels, material, and transfer points.

**Rioleringsgebied (several services).** Sewer service areas, realised and planned, per treatment plant, plus a flow-direction layer with from and to fields between areas.

**Afwateringseenheid, Peilgebied, Polder, Boezemgebied, KRW.** The drainage units, level areas, polders, boezem area and water bodies. Together with the watercourse and structure layers, these give the hydrological hierarchy.

**Meetlocatie (quantity and quality), TransportleidingMeetpunt, Zwemwaterlocatie.** Measurement locations, pipeline measurement points and bathing sites.

**Pilot_data_portaal_afvalwaterketen.** Detailed 2019 sewer data for Alphen aan den Rijn, Bodegraven-Reeuwijk and Waddinxveen, including overflows, pumps, and drainage surfaces.

Together these mean the sewer side of the chain is better mapped than I first thought. I have not seen the records themselves, so I do not know how complete the discharge and overflow layers are (for example, how many features have a discharge volume filled in).

## 4. What can be downloaded, and how

| Source | Format and method | Status |
|---|---|---|
| Map server layers (all services above) | Query as JSON, GeoJSON or PBF, in Dutch RD coordinates. The layers I checked also list WMS and WFS. Up to 1,000 to 2,000 records per request, so bigger layers need paging. GeoJSON converts to shapefile or GeoPackage in one step, and QGIS can load the services directly. | Verified in the service pages. Nothing downloaded. |
| Tile 15, WebService viewer | Rijnland's description says users can view, select and download. The layer group menus showed "No available actions", so the download probably sits in the selection and table tools. | Description read. Download not tested. |
| Tile 16, open data story | Described as explaining how to use the maps and download data. | Only chapter 2 read |
| Power BI dashboards (07, 10, 12, 14) | Public "publish to web" reports. Export options are usually disabled on these, so plan on asking for the underlying tables. | Not tested |
| Actuele Metingen (06) | Maps only. Readings are Hydronet's. No download or interface seen. | Ask Rijnland |
| Sidebar map apps (01 to 05, 08, 09) | The icons at the top right look like print/PDF and open-in-new-window. No data download seen. | Not tested |
| Shapefile files | No ready-made shapefile download found. Build from the map server. | |

## 5. Licence

Every item in the group carries the same notice in Dutch: without prior permission from the makers, it is not allowed to copy, share or reproduce text, images or other material from the page, because of possible copyright infringement. The notice sits on the map apps and the dashboards. Nothing I read states the terms of use for the map server data itself, although the gallery describes an "Open Data" group and a guide for GIS professionals. Before we use screenshots in the pitch or publish derived data, ask the challenge owner.

## 6. What this changes for the prototype

The candidate-source side of the tool becomes much stronger. Discharge points, overflow structures, effluent pumping stations and small treatment systems are all mapped as points, with links to receiving watercourses and sewer areas. That is the "where could it have come from" layer, and it comes from the water board's own registers.

The routing side is also better than I assumed. The watercourse layers, the structures, the level areas with summer and winter targets, the polders and the sewer flow-direction layer together support a network with pump-gated sub-catchments, with the caveat that I still saw no flow direction on the watercourse centrelines themselves.

The observation side is unchanged. The live stations (rain, level, flow, pump status, chloride and electrical conductivity, temperature) are the signal. The satellite layer applies to the 40 water bodies and to watercourses wide enough to resolve.

A workable first study area is still Alphen aan den Rijn and Bodegraven-Reeuwijk: the pilot sewer data, several named treatment plants (Alphen Kerk en Zanen, Alphen-Noord, Bodegraven), heavy dredging activity (a false-positive filter to model) and the large lakes nearby.

## 7. Questions to send to the challenge owner

Which of these layers may we download and reuse, and under what terms? Can we have the Actuele Metingen time series, or a Hydronet or Lizard access route, and from which date back? How complete are the discharge point and overflow layers, and does the discharge volume field hold real values? Is there any record of when overflows actually spilled? Do the watercourse centrelines carry flow direction anywhere? Is there a past incident with a known cause that we can use as a test case?

## 8. What I could not check

The chapters of the open data story beyond chapter 2, the individual tabs of the Power BI dashboards, whether the WebService viewer's download tools work, and the actual records in the discharge, overflow and measurement layers. The browser tabs became unresponsive during the last checks, so I relied on the service definitions for those.

## Sources

- [Rijnland op de kaart (gallery)](https://rijnland.maps.arcgis.com/apps/instant/filtergallery/index.html?appid=cc74a510ca1644d78dfb914e09cb1b5a)
- [Rijnland ArcGIS map server directory](https://rijnland.enl-mcs.nl/arcgis/rest/services)
- [Lozingspunt layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Lozingspunt/MapServer/0)
- [Overstortconstructie layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Overstortconstructie/MapServer/0)
- [Effluentgemaal layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Effluentgemaal/MapServer/0)
- [IBA layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Iba/MapServer/0)
- [Rioleringsgebied stroomrichting layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Rioleringsgebied_stroomrichting/MapServer/0)
- [Pilot data portaal afvalwaterketen](https://rijnland.enl-mcs.nl/arcgis/rest/services/Pilot_data_portaal_afvalwaterketen/MapServer)
- [Legger oppervlaktewater MapServer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Leggers/Legger_Oppervlaktewater_Vigerend/MapServer)
- [Meetlocatie waterkwantiteit layer](https://rijnland.enl-mcs.nl/arcgis/rest/services/Meetlocatie_waterkwantiteit/MapServer/0)
