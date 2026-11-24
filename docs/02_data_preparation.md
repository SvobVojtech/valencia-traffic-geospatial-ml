# Data preparation · Hito 2

The dataset has one row per road segment with geometry. The saved GeoParquet was inspected directly for dimensions, CRS, target summaries and missing values. Earlier-stage counts and spatial summaries below come from the recorded notebook outputs and control export.

## Target and spatial support

1. Filter hourly observations to the study window. The resulting panel contains 21,276 records, covering 394 segment keys and 54 distinct timestamps. The timestamp–segment pairs are unique and each segment has 54 eligible observations.
2. Calculate each segment's mean from nonnegative readings and retain targets with at least 50% valid coverage. There were 355 qualifying targets before joining to geometry.
3. Parse the supplied segment line geometries, reproject to ETRS89 / UTM zone 30N (EPSG:25830), measure length and build a 400 m buffer around each segment. Two qualifying segments lacked geometry and were omitted.
4. Keep the 392 distinct spatial segments and left-join the retained targets and contextual features by `gid`. Target and coverage fields remain null on the 39 application rows because the target table was filtered before joining.

## Feature construction

| Layer | Spatial operation | Outputs and interpretation |
| --- | --- | --- |
| Buildings | Intersect polygons with buffers and aggregate clipped areas and records by segment. | Clipped footprint area, built share, intersection count and count per km². The count describes intersection records and does not deduplicate cadastral buildings. |
| Motorised roads | Classify motorway, trunk and primary as principal, secondary and tertiary as secondary, and residential, living street, unclassified and service as local. Include links for the corresponding major classes and clip lines to buffers. | Length by class and principal length divided by total included length. The run retained 13,872 of 41,341 loaded road features and produced 82,800 clipped fragments. |
| Transport points | Count OSM transport points using a point-within-buffer spatial join. | Counts cover the supplied transport layer, which includes more than bus stops. |
| Cercanías stations | Reproject station coordinates and use a nearest spatial join from the segment line. Reduce tied matches to one minimum distance per segment. | Nearest-station distance in metres. |
| Population | Join 2023 population to 2026 municipal tracts on `CUSEC`, intersect tracts with buffers and sum each tract population multiplied by its intersected area fraction. | Estimated population and density per km² of full buffer area. A tract lacking a population match did not intersect any study buffer. All intersected tracts had counts. |

Buffer areas sum to 345.7 km², while their union covers about 50.5 km². These contextual features are calculated per segment. Adding them across segments would double-count shared areas. Empty mapped counts and absent road classes can take zero as a structural value. The final dataset has no undefined principal-road shares.

## Verified dataset statistics

| Measure | Value | Evidence |
| --- | --- | --- |
| Spatial rows × columns | 392 × 22 | Saved GeoParquet |
| Unique segment keys | 392 | Saved GeoParquet |
| Target-bearing / application rows | 353 / 39 | Saved GeoParquet; matches assignment reference counts |
| Median among spatial rows with targets | 677.04 veh/h | Saved GeoParquet; assignment reference is 677 veh/h |
| Mean among spatial rows with targets | 1,270.33 veh/h | Saved GeoParquet |
| Median before geometry join | 691 veh/h across 355 qualifying targets | Recorded notebook output, rounded as displayed |
| Candidate predictors with missing data | 0 of 13 | Saved GeoParquet |
| Recorded data-stage controls | 27 passed, 0 failed | Local control export |

The only missing columns are the target and three coverage fields, each missing on the same 39 application rows. Recorded controls covered the observation window, key uniqueness, coverage bounds, geometry and CRS, buffer areas, feature ranges and the geometry-join exclusions. The local CSV preserves recorded controls from the data-stage run.

## Reproducibility

The [portfolio notebook](../notebooks/01_data_preparation.ipynb) contains an independently written target calculation and an audit of the saved output. Raw file ingestion and the spatial construction code from the working notebook are omitted because they depend on course support code and adapt instructor-provided cells. The construction steps are described above. Exact source snapshots and availability are documented in the [data notes](../data/README.md). No model training, predictions or model diagnostics were performed at this stage.
