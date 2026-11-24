# Geospatial Traffic Modelling in Valencia

Can the urban context within 400 metres of a road segment help estimate its weekday afternoon traffic intensity in València? The goal is to assess whether these estimates could help compare streets where reliable traffic measurements are unavailable.

I am developing this ongoing project during my Erasmus semester at Universitat Politècnica de València (UPV), in the MSc course *Big Data y minería de datos geoespaciales*, academic year 2026/2027. The technical proposal (Hito 1) and analytical design (Hito 2) define the study and document dataset construction. Model fitting and evaluation are pending.

## Study design

| Parameter | Setting |
| --- | --- |
| Unit of analysis | One road segment, with context measured in a 400 m buffer around its line geometry |
| Observation period | March 2025, Monday to Friday at 17:00, 18:00 and 19:00, excluding 15–19 March inclusive because Fallas changes traffic patterns |
| Target | Arithmetic mean of valid hourly traffic readings per segment, in vehicles per hour (veh/h) |
| Minimum coverage | At least 50% of eligible readings, or 27 of 54 in this dataset |
| Coordinate reference system | ETRS89 / UTM zone 30N (EPSG:25830) for lengths, areas and spatial joins |

The input panel contains 54 eligible observations per segment. The target calculation excludes the missing-data sentinel `Lectura = -1` and retains valid zeros. Segments below the coverage threshold have no retained target.

## Data sources

| Source | Role in the study |
| --- | --- |
| Ajuntament de València traffic data | March 2025 hourly readings and road-segment geometries |
| OpenStreetMap contributors, via a Geofabrik extract | Building footprints, road network and mapped transport points |
| Instituto Nacional de Estadística (INE) | 2023 population counts and census tract boundaries dated 1 January 2026 |
| Renfe Open Data | Cercanías station coordinates |

The original analysis runs in the course environment with its supplied inputs and support code. This repository documents the study and its progress. The working notebook and input data are kept locally.

## Feature engineering

I intersected building polygons and motorised-road lines with each segment buffer and aggregated the clipped geometries. A point-in-polygon join counted mapped transport points, and a nearest join measured the distance from each segment line to the closest Cercanías station. I estimated population by weighting each contributing census tract's population by the fraction of its area inside the buffer.

The resulting dataset contains 13 candidate predictors.

| Group | Candidate predictors |
| --- | --- |
| Segment geometry | Segment length |
| Buildings | Building–buffer intersection count, clipped footprint area, built-up share and intersection density |
| Road network | Principal, secondary and local road lengths, and principal-road share of included road length |
| Transport | Mapped transport-point count and distance to the nearest Cercanías station |
| Population | Estimated buffer population and estimated population density |

The model will use predictors that can be computed for streets without traffic measurements. Counts of valid and eligible readings, coverage percentage and target availability are measurement controls. Segment IDs and descriptions are metadata, and the line geometry supports spatial analysis. These fields are excluded from the predictor set.

## Current progress

The data stage produced **392 road segments and 22 columns**. Of these, **353 segments have a retained target** and **39 are candidates for later estimation**. Two additional segments met the traffic coverage threshold but lacked geometry and were excluded. All 13 candidate predictors are populated for the 392 spatial rows, and all 27 recorded data-stage checks passed.

The assignment sheet gives **677 veh/h** as the reference median and **353 target-bearing / 39 application segments** as reference counts. The constructed dataset matches those counts. The notebook separately reports a descriptive median of **691 veh/h** for the 355 qualifying targets before the geometry join. The two medians refer to different stages of the workflow. There are no fitted models, validation scores, predictions or residual diagnostics yet.

## Limitations and evaluation

- Nearby segments can share much of their 400 m context. Random splits could overestimate performance on streets farther from training segments. I plan to compare random and spatial validation.
- The coverage rule may favour certain locations, road types or sensor conditions. I plan to compare the 353 retained segments and 39 application segments before interpreting estimates.
- Population interpolation assumes uniform density within each tract and uses only contributing València tracts. The population, traffic and boundary inputs come from different years. OpenStreetMap counts reflect mapped coverage.
- This target describes a specific set of weekday hours in one month. It has limited relevance to weekends, other seasons and event traffic.
- Building, road and population measures may be correlated. I plan to inspect feature redundancy during modelling.

Mean absolute error (MAE) in veh/h will be the primary metric. I will also report RMSE in veh/h and R², with variation across validation folds and separate results for random and spatial splits.

## Workflow checklist

- [x] Define the research question, traffic target and study parameters.
- [x] Construct the segment-level dataset and record data-quality checks.
- [x] Document candidate predictors, missingness, and leakage controls.
- [ ] Explore target and feature distributions and compare retained and application segments.
- [ ] Fit a baseline and candidate models.
- [ ] Compare random and spatial validation and examine residuals.
- [ ] Estimate traffic for application segments and produce maps.
- [ ] Document final findings and reproducibility steps.
