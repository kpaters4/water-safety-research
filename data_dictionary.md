# Data Dictionary

**Source:** [EPA Safe Drinking Water Information System (SDWIS)](https://catalog.data.gov/dataset/safe-drinking-water-information-system-sdwis-federal-reports-advanced-search-tool)
**Timeframe:** 1994–2016  
**Files:** 11 CSVs. A collection of datasets covering NO₃ contamination in the continental United States.
- DS01 - SDWIS_NO3_Violations_Over_Time_1994-2016_FINAL
- DS02 - SDWIS_NO3_Violation_Duration_State_1994-2016_FINAL
- DS03 - Mean_Annual_NO3_Violations_County_1994-2016_FINAL
- DS04 - Mean_Annual_NO3_Pop_Served_County_1994-2016_FINAL
- DS05 - SDWIS_NO3_Violations_County_1994-2016_FINAL
- DS06 - SDWIS_NO3_Pop_Served_County_1994-2016_FINAL
- DS07 - SDWIS_GW_NO3_Violations_County_1994-2016_FINAL
- DS08 - SDWIS_SW_NO3_Violations_County_1994-2016_FINAL
- DS09 - SDWIS_Percent_GWSW_Violations_County_1994-2016_FINAL
- DS10 - SDWIS_Percent_GW_NO3_Violations_County_1994-2016_FINAL
- DS11 - SDWIS_Percent_SW_NO3_Violations_County_1994-2016_FINAL

---

## DS 01 — `SDWIS_NO3_Violations_Over_Time_19942016_FINAL.csv`
**National NO₃ Violations — Annual Time Series**

One row per calendar year. Tracks how many public water systems were in violation of the nitrate MCL, the share of active systems affected, and the population exposed — at the national level.

- **Rows:** 23 · **Columns:** 7

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `YEAR` | Integer | TEMPORAL | Calendar year of observation. | 1994, 2010 | Range: 1994–2016. Primary join key for temporal analysis. |
| `Systems_in_Violation` | Integer | METRIC | Count of public water systems that recorded at least one nitrate MCL violation during the year. | 476 | Absolute count; use with `Total_Active_Systems` for a rate. |
| `Perc_in_Violation` | Float | METRIC | Percentage of active systems in violation that year (proportion, not 0–100 scale). | 0.2788 | Multiply ×100 for display. Derived as `Systems_in_Violation / Total_Active_Systems`. |
| `Total_Active_Systems` | Integer | METRIC | Total number of active public water systems registered in SDWIS for that year. | 170757 | Denominator for `Perc_in_Violation`. Declines slightly over time. |
| `Total_Pop_Served_Active_Systems` | Integer | METRIC | Total U.S. population served by all active public water systems in the given year. | 271062524 | Denominator for national exposure calculations. |
| `Population_Served` | Integer | METRIC | Total population served by systems that were in violation of the nitrate MCL that year. | 198187 | Highly variable year-to-year; reflects both violation counts and system size. |
| `PopServed_Percent_UsPop` | Float | METRIC | Population served by violating systems as a fraction of the total U.S. population. | 0.0761 | Multiply ×100 for percent. Useful for national exposure trend analysis. |

---

## DS 02 — `SDWIS_NO3_Violation_Duration_State_19942016_FINAL.csv`
**Violation Duration by State (Primacy Agency)**

One row per state primacy agency. Summarizes how long individual violations lasted (in years), capturing chronic versus acute exceedances.

- **Rows:** 45 · **Columns:** 3

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Primacy.Agency` | String | GEO | Name of the state primacy agency responsible for enforcing drinking water standards (generally corresponds to a U.S. state). | California, Texas | 45 agencies represented; does not include all 50 states (only those with nitrate violations on record). |
| `Max_Duration_Year` | Float | METRIC | Maximum duration of a single nitrate violation event within the state, expressed in decimal years. | 2.252 (Texas) | Values > 1 indicate a violation persisting longer than one year. Texas (2.25 yr) and Oklahoma (1.13 yr) are notable outliers. |
| `Mean_Duration_Year` | Float | METRIC | Average duration of all nitrate violation events within the state, expressed in decimal years. | 0.353 (Arizona) | Most states average well under 1 year. Can serve as a state-level chronic-exposure feature. |

---

## Shared Geographic Identifier Fields (DS 03–DS 11)

> **Note:** All county-level files carry the same 8-column geographic identifier block. These fields are described once here and apply to every county-level dataset (DS 03–DS 11). Each file then adds one unique measurement column. Use `FIPS` (5-digit integer) or `FIPS2` as the primary join key across datasets and for choropleth mapping.

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `State_County` | String | ID | Composite key combining two-letter state abbreviation and county name, separated by an underscore. | AK_Anchorage Municipality | Useful for human-readable labeling; use FIPS for programmatic joins. |
| `State` | String | GEO | Two-letter U.S. state/territory postal abbreviation. | AK, CA, TX | Standard USPS abbreviations. |
| `STATE_FIPS` | String/Integer | ID | Two-digit FIPS code identifying the state. | "02" (Alaska), "06" (California) | Zero-padded string in some files; cast carefully when joining. |
| `CNTY_FIPS` | String/Integer | ID | Three-digit FIPS code identifying the county within a state. | "013", "020" | Zero-padded; must be combined with `STATE_FIPS` for a unique national identifier. |
| `COUNTY` | String | GEO | Full county or county-equivalent name (including "County," "Parish," "Borough," etc.). | Aleutians West Census Area | May differ slightly from Census Bureau official names; use FIPS for joins. |
| `COUNTY2` | String | GEO | Alternate or duplicate county name field. Appears in DS 05–DS 11; identical to `COUNTY` in all observed rows. | Aleutians West Census Area | Likely a legacy R/GIS artifact from the original data preparation. Not present in DS 03–DS 04. |
| `FIPS` | Integer | ID | Standard 5-digit FIPS county code (`STATE_FIPS` concatenated with `CNTY_FIPS`). Primary geographic join key. | 02016, 06037 | Use this as the primary key when merging county-level datasets. Unique nationally. |
| `FIPS2` | String | ID | String version of the 5-digit FIPS code, prefixed with "f" (e.g., `f02016`). Used for GIS / shapefile attribute joins. | f02016, f06037 | The "f" prefix prevents Excel/GIS tools from dropping leading zeros. Strip the "f" before numeric operations. |

---

## DS 03 — `Mean_Annual_NO3_Violations_County_19942016_FINAL.csv`
**Mean Annual NO₃ Violations by County**

One row per county. Reports the average number of nitrate MCL violations per year across the 1994–2016 study period, aggregated to the county level.

- **Rows:** 1,085 · **Columns:** 8

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `mean_viol_cnty` | Float | METRIC | Mean number of nitrate MCL violations per year for all public water systems within the county, averaged over 1994–2016. | 0.043, 0.130 | Key target-related feature. Only counties with at least one violation are included (1,085 of ~3,235 counties). |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 04 — `Mean_Annual_NO3_Pop_Served_County_19942016_FINAL.csv`
**Mean Annual Population Served by Violating Systems — by County**

One row per county. Reports the mean annual population served by systems that were in nitrate violation, providing a sense of exposure magnitude per county over the study period.

- **Rows:** 1,085 · **Columns:** 8

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `cnty_pop_mean` | Float | METRIC | Mean annual number of people served by nitrate-violating public water systems in the county, averaged across 1994–2016. | 250.4, 1840.0 | Indicates public health exposure scale. Useful as a population-weighting factor. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 05 — `SDWIS_NO3_Violations_County_19942016_FINAL.csv`
**Total NO₃ Violation Frequency by County (All Source Types)**

One row per county for all 3,235 U.S. counties. Records the cumulative count of nitrate violations across all water system source types (groundwater + surface water combined) over the full study period.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Freq` | Integer | METRIC | Cumulative count of all nitrate MCL violation-years recorded for systems in the county across the entire 1994–2016 study period (groundwater + surface water combined). | 0, 1, 14 | Many counties have Freq = 0 (no violations). Compare with DS 07 (GW only) and DS 08 (SW only) to decompose by source. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 06 — `SDWIS_NO3_Pop_Served_County_19942016_FINAL.csv`
**Total Population Served by Violating Systems — by County**

One row per county for all 3,235 U.S. counties. Records the cumulative population served by nitrate-violating systems across all years and source types.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Pop_Served` | Integer | METRIC | Cumulative number of people served by nitrate-violating public water systems in the county across the full 1994–2016 study period. | 0, 697, 45000 | Sum across all violation-years, not a unique-person count. Normalize by years active or total population for comparisons. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 07 — `SDWIS_GW_NO3_Violations_County_19942016_FINAL.csv`
**Groundwater-Sourced NO₃ Violation Frequency by County**

One row per county. Isolates nitrate violations from groundwater-sourced (GW) public water systems only — wells and springs.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Freq` | Integer | METRIC | Cumulative count of nitrate MCL violation-years for groundwater-sourced systems in the county, 1994–2016. | 0, 1, 8 | Groundwater nitrate elevation is typically associated with agricultural (fertilizer) and septic contamination. Pair with DS 08 to compare GW vs. SW. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 08 — `SDWIS_SW_NO3_Violations_County_19942016_FINAL.csv`
**Surface Water-Sourced NO₃ Violation Frequency by County**

One row per county. Isolates nitrate violations from surface water-sourced (SW) public water systems only — rivers, lakes, and reservoirs.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Freq` | Integer | METRIC | Cumulative count of nitrate MCL violation-years for surface water-sourced systems in the county, 1994–2016. | 0, 1, 3 | Surface water nitrate loading often linked to runoff and upstream land use. Typically lower frequency than GW violations nationally. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 09 — `SDWIS_Percent_GWSW_Violations_County_19942016_FINAL.csv`
**Percent of Systems in Violation — GW + SW Combined, by County**

One row per county. Expresses the share of all systems (groundwater and surface water combined) that were in nitrate violation over the study period, as a proportion.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Perc_Viol` | Float | METRIC | Proportion of all public water systems (GW + SW) in the county that had at least one nitrate MCL violation, 1994–2016. Expressed as a decimal (0–1). | 0.0, 0.621 | Rate metric; normalizes for county system count. Complementary to absolute `Freq` in DS 05. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 10 — `SDWIS_Percent_GW_NO3_Violations_County_19942016_FINAL.csv`
**Percent of Groundwater Systems in Violation — by County**

One row per county. Expresses the share of groundwater-sourced systems in the county that were in nitrate violation over the study period.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Perc_Viol` | Float | METRIC | Proportion of groundwater-sourced public water systems in the county that had at least one nitrate MCL violation, 1994–2016. Expressed as a decimal (0–1). | 0.0, 0.75 | GW-specific rate; isolates well/spring systems. High values may indicate agricultural contamination or shallow aquifer vulnerability. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

## DS 11 — `SDWIS_Percent_SW_NO3_Violations_County_19942016_FINAL.csv`
**Percent of Surface Water Systems in Violation — by County**

One row per county. Expresses the share of surface water-sourced systems in the county that were in nitrate violation over the study period.

- **Rows:** 3,235 · **Columns:** 9

| Field | Type | Category | Description | Example | Notes |
|-------|------|----------|-------------|---------|-------|
| `Perc_Viol` | Float | METRIC | Proportion of surface water-sourced public water systems in the county that had at least one nitrate MCL violation, 1994–2016. Expressed as a decimal (0–1). | 0.0, 0.33 | SW-specific rate. Generally lower than GW equivalent nationally. High values may indicate intensive upstream agricultural runoff. |

*+ shared geographic fields: `State_County`, `State`, `STATE_FIPS`, `CNTY_FIPS`, `COUNTY`, `COUNTY2`, `FIPS`, `FIPS2` (see shared block above)*

---

AI Disclaimer. Generative AI was used to format this page and clarify data descriptions.