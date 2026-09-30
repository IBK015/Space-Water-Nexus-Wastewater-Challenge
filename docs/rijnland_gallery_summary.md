# Rijnland "op de kaart" gallery: what is in the 16 tiles and what it means for us

Note: superseded by rijnland_16_tiles_and_downloads.md. That note corrects two statements below: the map server does have discharge point and overflow layers (Lozingspunt, Overstortconstructie), and the Actuele Metingen readings come from Hydronet.

I opened all 16 tiles from the tabs you had open. The map canvases did not draw their basemaps in my browser, so for the map-only tiles I could read only titles, legends and sidebar text, not the data behind them. Where something was small print in a screenshot, I say so. Anything marked "ask" needs confirmation from Water Natuurlijk Rijnland.

## The short version

Tile 06, "Actuele Metingen", is almost certainly the station data you mentioned. It publishes rain, water levels, pump flow, pump on/off status, chloride, electrical conductivity (EC) and water temperature. That is a good fit for a twin: none of it measures organic pollution or nutrients directly, but EC and temperature can shift when treated or untreated wastewater enters a small water, and pump status tells you when water is being moved. Whether they do so in a way we can detect is something to test with the real data, not assume.

Tile 11 shows the wastewater transport chain (treatment plants, influent pumping stations, pipelines). I saw no layer for outfalls or sewer overflows, which is the piece we most need.

Tiles 03 and 16 show that the watercourse layer carries a width attribute. That lets us mark which waters are wide enough for Sentinel-2 to say anything about.

## Tile by tile

| # | Tile | What I found | Relevance |
|---|---|---|---|
| 01 | Legger regionale keringen | Map of regional flood defences. Legend: regional defence axis, boezem embankment, polder embankment, Rijnland boundary. | Low. Useful only for the polder versus boezem structure. |
| 02 | Legger primaire waterkeringen | Map of primary flood defences, no legend. | Low |
| 03 | Legger oppervlaktewater | Map of the surface-water register, no legend shown. The open-data story (tile 16) shows its "Watergang as" objects carrying attributes such as BREEDTE (19.26 in the example) and BODEMBREEDTE (6.82), units not stated. | High. This is the channel network for the graph, and the width lets us mask by satellite visibility. |
| 04 | Legger ondersteunende kunstwerken | Map of supporting structures, no legend. I could not see attributes. | High for connectivity if it has flow direction (ask). |
| 05 | Baggeren project | Map of dredging projects, no legend. | Medium. Dredging stirs up sediment, so it is a known false-positive source for turbidity in satellite images. |
| 06 | Actuele Metingen | Eight layers, described below. | Very high |
| 07 | Waterkwaliteit (Power BI) | Tabs for general information, phosphorus, nitrogen, chloride, zinc and copper. Measurement points are tested against KRW norms using the summer average of the previous period. Water types run from M1a (fresh, buffered ditches) and M1b (non-fresh ditches) to M3 (buffered regional canals) and others. I read only the overview, not the individual chemistry tabs. | Medium to high as a historical baseline |
| 08 | Peilbesluiten met peilafwijkingen | Map of water-level decisions with deviations, no legend. | Medium as flow and level context |
| 09 | KRW waterlichamen Rijnland | Map of the Water Framework Directive water bodies. | Medium to high. These are the larger waters where satellite data is most plausible. |
| 10 | KRW dashboard "Toestand 2025" (Power BI) | Home, Beoordeling, Maatregelen, Trend, ESF-analyse, Begrippenlijst. Rijnland has 40 KRW water bodies, and the KRW covers waters above 50 hectares. Updated once a year, in May or June. The small print appears to say the data run to late 2023 (hard to read). | Medium. It shows how coarse the current reporting is. |
| 11 | Afvalwaterketen | Experience with 12 layers: Afsluiter (valves), Afvalwaterzuivering (treatment plants, sized by biological capacity: under 15,000, 15,000 to 150,000, over 150,000), Inspectieput, KathodischeBescherming, Mantelbuis, Ontluchter, PIG Lanceerinrichting, Rioolgemaal influent (with an owner field), Transportleiding, Transportleidingsegment Rijnland, Transportleidingsegment noodleiding (emergency pipe), Zuiveringseenheid. The first table showed 253 features. | High for candidate discharge points. This is the transport network to the plants, not the sewers or outfalls. |
| 12 | Agrarisch Meetnet (Power BI) | Monthly measurements since 2014 at fixed locations in areas dominated by arable farming, bulbs, tree nurseries, greenhouses or dairy. Monitors crop protection products and nutrients. Three reports: pesticide concentrations, annual norm exceedances, nutrient concentrations. | Medium to high. Helps separate agricultural signals from wastewater. |
| 13 | Waterbeheerprogramma WBP6 | Policy site for the 2022 to 2028 water management programme. | Low, context for the pitch |
| 14 | KRW visstand dashboard (Power BI) | Fish monitoring per water body, nine pages. | Low |
| 15 | WebService Rijnland | Experience listing the web service layers by category: Afvalwater, Gebied extern, Gebied Rijnland, Keringen, Kunstwerken, Legger Rijnland (more below, I saw six). Its start button links back to the gallery. | High for access. This is the catalogue of layers we can pull into QGIS or Python. |
| 16 | Open Data voor GIS-professionals | Story collection with eight chapters: Introductie, Wat u wilt en wat u kan, Over de kaarten, Oppervlaktewater, Regionale keringen, Kunstwerken vigerend, Baggerwerkzaamheden, Peilbesluiten. I read chapter 2 (attribute pop-ups). The tab froze before I could read the other chapters. | High for data access |

## Tile 06 in detail

The portfolio has eight views.

Rain (mm per day) comes from Rijnland's own rain gauges, placed at pumping stations and treatment plants. Boezem water levels are managed to a fixed target, NAP -0.61 m in summer and -0.64 m in winter. Polder water levels are measured at many locations. Flow (debiet) is given for pumping stations: polder pumps run from about 10 to 1,275 m³ per minute, and four large boezem pumping stations (Spaarndam, Halfweg, Gouda, Katwijk) run from 32 to 94 m³ per second, 194 m³ per second in total. A positive value means water going into the boezem, and a negative value means discharge out of it, to the Noordzeekanaal, the Hollandsche IJssel or the North Sea. Pump status shows whether more than 350 remotely operated pumping stations are running. Chloride and EC are indicators of salinisation: chloride typically has a norm of 200 mg per litre, EC has no norm, and values range from about 50 mg per litre (200 µS/cm) in nature areas to as much as 2,000 mg per litre (7,000 µS/cm) in deep reclaimed polders such as the Haarlemmermeer. Water temperature is recorded wherever level or EC sensors are installed.

What I did not verify: how often these update, whether history is available, whether there is an API, and how many stations have EC and temperature. The page says "actuele" (current) but gives no interval.

## What this changes in our design

**The network is pump-gated.** Polders drain toward their pumping station, which lifts water into the boezem. That gives a natural structure for the twin: each polder is a sub-catchment with one outlet, and the boezem is a mixing body with four large outlets. Pump status and flow then say when water actually moves. This is my inference from the descriptions, and the structure attributes will show how well it holds.

**Our station signals are EC, temperature, level, flow and pump status.** A dry-weather change in EC or temperature with no rain and no pump activity is a candidate signal. Whether wastewater produces a visible step in these variables in Rijnland's waters is a hypothesis we can test with the historical data. Do not claim it in the pitch until we have.

**Width gives an observability mask.** If the attribute holds up, we can restrict any satellite statement to watercourses wider than a few pixels and to the KRW water bodies, and show everything else as "not observable from space." A first cut might be about three Sentinel-2 pixels (roughly 30 m), which is my suggestion, not a Rijnland figure.

**Two false-positive filters are already in the gallery.** Dredging projects (tile 05) explain turbidity that is not wastewater. The agricultural network (tile 12) helps explain nutrient or pesticide peaks.

**Discharge candidates are partly there.** Treatment plants and influent pumping stations are mapped. Outfalls and overflows are not visible, so ask.

## Cautions

The Power BI reports and the Actuele Metingen page both carry a notice that copying their text, images or other material without the makers' permission is not allowed. Use the data the challenge owner hands to participants and ask whether screenshots of these dashboards can appear in the pitch.

The open-data story presents the layers as open data for GIS professionals, but I did not see the licence terms.

## Questions to send

Which stations measure EC and temperature, and at what interval? Is there history, and how far back? Is there an API or a feature service URL for the Actuele Metingen layers? Do the structure layers have flow direction and connectivity? Where are outfalls, overflows and industrial permitted discharge points, and can we have them as a layer? Can we have the full list of layers in the WebService Rijnland catalogue? What are the licence terms for the data and dashboards? Is there a past incident with a known cause that we can replay?

## What I did not read

The individual chemistry tabs of tile 07, the sub-pages of tile 10, chapters 3 to 8 of tile 16, the full layer list of tile 15, and the attribute tables behind tiles 03, 04, 05, 08 and 09.
