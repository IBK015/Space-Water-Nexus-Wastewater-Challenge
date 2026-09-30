# Open wastewater discharge data for the study area

Checked whether open, downloadable wastewater discharge data exists for the Rijnland study area (Alphen aan den Rijn, Bodegraven-Reeuwijk and neighbours), beyond what Rijnland's own map server offers. Short answer: yes, and the best source is national rather than regional.

## Richtlijn Stedelijk Afvalwater (RSA), via PDOK

Rijkswaterstaat's Dutch reporting layer for the EU Urban Wastewater Treatment Directive is published on [PDOK](https://www.pdok.nl/introductie/-/article/kaderrichtlijn-stedelijk-afvalwater) under CC0. Four feature layers: `rsa_agglomeraties` (service areas), `rsa_rwzi` (treatment plants), `rsa_lozingspunten` (discharge points), and `rsa_kwetsbaargebied` (sensitive receiving areas). Served as WMS and WFS at [service.pdok.nl](https://www.pdok.nl/ogc-webservices/-/article/kaderrichtlijn-stedelijk-afvalwater), plus an OGC API and a bulk ATOM download in CSV, GeoJSON, GML, shapefile and GeoPackage. Covers 2016 through 2024, nationally, so it includes the Rijnland-area plants already found on Rijnland's own Afvalwaterketen map (Alphen aan den Rijn, Bodegraven, Gouda, Leiden and the rest).

Why this matters beyond just having more data: it's a second, independently reported source for discharge points, which lets the team cross-check Rijnland's own Lozingspunt layer, whose DEBIET (discharge volume) field completeness was flagged as unverified in the earlier layer catalogue, against a dataset built specifically for EU compliance reporting. Layering `rsa_lozingspunten` over Rijnland's Lozingspunt for the Alphen aan den Rijn / Bodegraven-Reeuwijk pilot area is a concrete, doable cross-check, and "we verified our source data against an independent national register" is a good line for the pitch.

## CBS process and sludge datasets

Two data.overheid.nl datasets from CBS: [treatment-plant process data](https://data.overheid.nl/dataset/900-zuivering-van-stedelijk-afvalwater--procesgegevens-afvalwaterbehandeling) (pollutant inflow and outflow, treatment efficiency, back to 1981) and a related sludge-disposal dataset. Both open under CC-BY 4.0, accessible via CBS's OData API. Both are aggregated by river basin district or nationally, not broken out per named plant, so they're useful as a sanity-check baseline ("is this plant's efficiency in a normal range") rather than for pinpointing a source at Rijnland's scale.

## Emissieregistratie / Watson: the interesting, caveated one

The national [Emissieregistratie](https://www.emissieregistratie.nl/data/watson-rwzi-emissiedata/toelichting-watson-database) system explicitly models discharges including from combined-sewer overflows, across roughly 1,400 substances and 332 treatment plants, with data from 1990 to 2024 (over 600,000 measurement records). This is the closest thing found anywhere to overflow-discharge data, which matters because "has any overflow actually spilled, and when" has been an open question since the first Rijnland gallery review.

The caveat is real: these are calculated emission estimates built for regulatory reporting, not measured events. The effluent-calculation factsheet explicitly says it distributes national totals across municipalities for most pollutants, rather than tracking individual named plants. So this is a plausible source for a historical baseline of expected overflow activity by area, not a log of confirmed incidents. Whether the Watson web application itself lets a user drill into one named plant wasn't confirmed from the documentation alone, worth five minutes in the actual app before relying on it.

## What's still missing

None of these sources give real-time discharge data, or a record of confirmed illegal or unusual discharge events with a known cause. That gap hasn't closed, and it's still worth asking Marc Minnee or Kees Buskermolen directly rather than assuming it exists somewhere unfound.

## A practical access note

PDOK is standard national infrastructure, not Rijnland's smaller ArcGIS server, but this session's cloud sandbox has had trouble with raw outbound connections to `pdok.nl` domains before, the same block that stopped the Rijnland download earlier. Reading PDOK's documentation pages worked fine just now, so the block may be narrower than "everything," but pulling the actual WFS data is safest tested directly in a browser or QGIS on a machine that isn't behind that same restriction, rather than assumed to work from here.

## Sources

- [PDOK: Richtlijn Stedelijk Afvalwater (RSA)](https://www.pdok.nl/introductie/-/article/kaderrichtlijn-stedelijk-afvalwater)
- [PDOK: RSA OGC web services](https://www.pdok.nl/ogc-webservices/-/article/kaderrichtlijn-stedelijk-afvalwater)
- [Data overheid: RSA EU2022 dataset record](https://data.overheid.nl/dataset/46967-richtlijn-stedelijk-afvalwater---rioolwaterzuiveringsinstallaties-nederland-eu2022)
- [CBS: Zuivering van stedelijk afvalwater, procesgegevens](https://data.overheid.nl/dataset/900-zuivering-van-stedelijk-afvalwater--procesgegevens-afvalwaterbehandeling)
- [Emissieregistratie: Watson RWZI database explanation](https://www.emissieregistratie.nl/data/watson-rwzi-emissiedata/toelichting-watson-database)
- [Emissieregistratie: RWZI effluent factsheet](https://legacy.emissieregistratie.nl/erpubliek/documenten/06%20Water/01%20Factsheets/31B%20-%20Factsheet%20effluenten%20(berekend)_20240603.pdf)
