# Data sources and availability

The analysis used course-supplied snapshots from the organisations below. Publisher pages identify upstream sources, and some current downloads differ from the supplied files. The repository contains no input data, row-level derived data or course export.

| Source used | Study input | Licence and availability |
| --- | --- | --- |
| Ajuntament de València | Hourly traffic CSVs and segment lines, March 2025 | The public traffic layer lists CC BY 4.0. The linked listing describes the current layer. The exact packaged March 2025 panel and its archive location have not been independently verified. [City catalogue](https://opendata.vlci.valencia.es/es/dataset/intensitat-transit-trams-intensidad-trafico-tramos), [national catalogue licence entry](https://datos.gob.es/es/catalogo/l01462508-intensidad-trafico-tramos) |
| OpenStreetMap contributors, via Geofabrik | Buildings, roads and transport points in a local GeoPackage | ODbL 1.0 applies to OpenStreetMap data. The exact extraction date and processing of the supplied GeoPackage are unverified. [OpenStreetMap attribution and licence](https://www.openstreetmap.org/copyright), [Geofabrik downloads](https://download.geofabrik.de/europe/spain.html) |
| Instituto Nacional de Estadística (INE) | Census tract boundaries dated 1 January 2026 | The source provides census-tract cartography. The milestone records CC BY 4.0 for the course copy. Check the release-specific credit and reuse terms for the exact download. [INE census tract cartography](https://www.ine.es/dyngs/DAB/es/index.htm?cid=1389) |
| Instituto Nacional de Estadística (INE) | 2023 population from demographic indicators, table 31258 | INE's general statistical-data terms are CC BY 4.0 unless otherwise indicated. The milestone notes list CC BY-SA 4.0 for the course copy. The exact export terms remain unverified. [Table 31258](https://www.ine.es/jaxiT3/Tabla.htm?t=31258), [INE reuse terms](https://www.ine.es/aviso_legal/) |
| Renfe Open Data | Cercanías station coordinates in the Valencia area | The upstream station listing is CC BY 4.0. The exact filtered station snapshot used in the course has not been independently located. [Renfe station listing](https://data.renfe.com/es/dataset/estaciones-listado-completo) |

The [portfolio notebook](../notebooks/01_data_preparation.ipynb) can audit the saved result when the local GeoParquet is available. Rebuilding that result requires the original snapshots and a replacement for the course-dependent ingestion and spatial preparation code.
