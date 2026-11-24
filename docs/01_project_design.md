# Project design · Hito 1

## Research question

Can the urban context within 400 metres of a road segment help estimate its weekday afternoon traffic intensity in València? The intended application is comparing segments where a reliable afternoon target is unavailable. The study uses segments in the supplied traffic inventory, so any extension to streets outside that inventory would need separate assessment.

## Study definition

| Design choice | Setting |
| --- | --- |
| Observation unit | One road segment identified by `gid` |
| Spatial context | 400 m around the full segment line, measured in EPSG:25830 |
| Eligible time window | Monday to Friday in March 2025 at hours 17, 18 and 19, excluding 15–19 March inclusive for Fallas |
| Response | `intensidad`, the per-segment arithmetic mean of valid `Lectura` readings, in vehicles per hour (veh/h) |
| Target availability | At least 50% valid eligible readings, equivalent to 27 of 54 in this panel |

The denominator includes every eligible observation before filtering on reading validity. The missing-value sentinel `-1` is excluded, and a zero reading is valid. Segments below the threshold have no retained target. The assignment sheet gives a reference median of 677 veh/h and reference counts of 353 target-bearing and 39 application segments. [Data preparation](02_data_preparation.md) reports the measured counts and medians separately.

## Candidate predictors

All 13 candidate predictors come from segment geometry or contextual sources available without using the target readings.

| Group | Fields and units |
| --- | --- |
| Segment | `longitud_tramo_m` (m) |
| Buildings | `n_edificios` (intersection count), `superficie_edificada_m2` (m²), `prop_edificada` (share), `dens_edificios_km2` (intersections/km²) |
| Roads | `long_via_principal`, `long_via_secundaria`, `long_via_local` (m), `prop_via_principal` (share) |
| Transport | `n_transporte` (points), `dist_estacion_m` (m) |
| Population | `poblacion_estimada` (estimated residents), `densidad_poblacion_km2` (estimated residents/km²) |

Coverage counts and percentages and the target-availability flag describe the measurement process and are excluded from the feature set. Segment identifiers, descriptions and geometry support joins, interpretation and spatial validation.

## Initial hypotheses

- Hito 1 anticipated a moderate R² of roughly 0.3–0.6. Local context may explain part of the variation, while trips also depend on origins, destinations and network conditions beyond a 400 m buffer.
- Principal-road length was the expected strongest candidate because major roads carry traffic between parts of the city.
- Narrow central streets near main roads were expected to produce larger errors. Nearby segments can have similar buffers despite different access rules or street functions.

These were initial expectations. No fitted model, feature ranking or residual analysis is available.

## Methodological risks and planned evaluation

- Overlapping buffers create spatial dependence. Compare random and spatial validation to assess how much performance changes when nearby segments are held apart.
- The minimum coverage rule can select for better-observed road types or locations. Compare the context and spatial distribution of target-bearing and application rows before interpreting estimates.
- Areal interpolation assumes uniform population within each tract. The 2023 population data and 2026 boundaries may not align perfectly, and the traffic observations cover a narrow period in 2025.
- Closely related built-area, road and population measures may carry redundant information. Examine this before interpreting individual features.

Use MAE in veh/h as the primary metric, with RMSE and R² as complements. Report variability across folds. Fitting, validation and estimation remain future work.
