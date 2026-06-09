# Seattle911
# Seattle 911 CARE-Eligible Calls Analysis

## CARE as Public Health Infrastructure

This project analyzes Seattle’s 2025 911 Computer-Aided Dispatch (CAD) data to identify where CARE-style alternative response systems could most effectively complement traditional police response and expand equitable access to non-punitive, human-centered crisis support.

The analysis focuses on Seattle’s CARE response model and asks:

> Where do care-relevant 911 calls occur, how are they currently handled, and which neighborhoods may benefit most from targeted CARE expansion?

Using public 911 dispatch data, neighborhood-level vulnerability measures, and a transparent call-type classification framework, this project builds a data-driven baseline for understanding alternative response opportunities across Seattle.

---

For the full methodology, intermediate checks, visualizations, and detailed findings, please see the personal report here:/Users/yeshid/projects/seattle-care/Personal Note on 911 Analysis.pdf


Please see the slides for more structured presentation: https://canva.link/ajvut2298ffora6


## Project Motivation

Emergency response systems often handle incidents that are not purely criminal or technical. Many calls involve behavioral health crises, welfare checks, substance use, missing persons, public distress, or other social needs that may not be fully resolved through a single emergency dispatch.

This project treats CARE not only as an alternative emergency response model, but as a form of public health infrastructure.

The guiding idea is:

> Emergency response solves the moment; care addresses the trajectory.

The goal is not to claim that every care-relevant call should replace police response. Instead, this analysis identifies where care-based response could plausibly support the system through expanded coverage, co-response, field handoffs, or targeted deployment pilots.

---

## Repository Note: Data Not Included

Large raw and processed datasets are **not included** in this GitHub repository because several files exceed GitHub’s 100 MB file limit.

Excluded files include raw CAD exports and large processed joins such as:

```text
Data/
data_processed/
*.csv
*.parquet
```

To reproduce the analysis, users should download the source data directly from public data portals and regenerate the processed files locally.

---

## Data Sources

### 1. Seattle 911 CAD Data

The core dataset is Seattle 911 Computer-Aided Dispatch data for 2025.

The raw CAD data contains multiple rows per incident because each responding unit can generate a separate record. To avoid double-counting multi-unit activity, the data is collapsed to one row per unique `cad_event_number`.

### 2. Seattle Racial and Social Equity Index

Neighborhood vulnerability is measured using Seattle’s Racial and Social Equity (RSE) Composite Index and sub-indices.

RSE domains used in this analysis include:

* Race, English proficiency, and origins
* Socioeconomic disadvantage
* Health disadvantage
* Composite RSE score

The RSE data is joined to dispatch geographies using Community Reporting Areas (CRAs), with population-weighted aggregation where neighborhoods span multiple census tracts.

Source: Seattle Open Data — Racial and Social Equity Composite Index
https://data.seattle.gov/dataset/Racial-and-Social-Equity-Composite-Index-Current/x5s4-2aie/about_data

---

## Core Research Questions

This project explores five main questions:

1. **System baseline:** What share of Seattle 911 incidents currently receive SPD-only, CARE-only, or SPD/CARE co-response?

2. **CARE eligibility:** What share of incidents are clearly or potentially care-relevant based on final call type?

3. **Response alignment:** Are CARE responses reaching the types of calls they are designed to address?

4. **Equity alignment:** Is Tier 2 CARE-eligible call burden higher in neighborhoods with higher RSE vulnerability?

5. **Strategic deployment:** Which neighborhoods show high Tier 2 demand but relatively low CARE involvement?

---

## Data Processing Overview

### 1. Logical Deduplication

The raw CAD data contains multiple records for many incidents because different units may respond to the same event.

Processing steps:

* Start with raw 2025 CAD records.
* Group by `cad_event_number`.
* Retain one representative row per event.
* Prioritize records with finalized call type and latest operational timestamps.
* Validate that each unique incident is preserved exactly once.

Summary:

| Stage                            |   Count |
| -------------------------------- | ------: |
| Raw CAD records                  | 546,208 |
| Unique CAD incidents             | 324,386 |
| Collapsed duplicate unit records | 221,822 |

The final event-level dataset contains one row per unique incident.

---

## CARE Eligibility Framework

A CAD event is considered care-eligible when:

* The response need is human-centered, not purely technical.
* The underlying issue is unlikely to be fully resolved by a single emergency dispatch.
* Follow-up, coordination, stabilization, or non-punitive intervention may be relevant.

This project uses a transparent three-tier classification framework based on `final_call_type`.

### Tier 0 — Traditional SPD

Calls that are primarily violent, criminal, property-related, weapon-related, or otherwise require traditional police response.

Examples include:

* Weapons
* Assault
* Robbery
* Burglary
* Theft
* Homicide
* Domestic violence
* Shots fired

### Tier 1 — Potential CARE

Boundary-category calls where care may be relevant, but police presence may still be needed depending on risk, immediacy, or scene conditions.

Examples include:

* Disturbances
* Assist public
* Intoxication
* Nuisance
* Harassment
* Juvenile/runaway
* Trespass
* Encampment-related calls
* Suspicious person/circumstances

### Tier 2 — Clearly CARE

Calls where behavioral health, welfare, crisis stabilization, or non-punitive support is plausibly central to the incident.

Examples include:

* Crisis complaints
* Welfare checks
* Suicide-related calls
* Mental health calls
* Down person
* Overdose
* Missing/found person
* Casualty
* Detox
* Child/elder welfare concerns
* CARE/CCR-only categories

Important caveat:

> This framework defines **eligibility**, not **operational appropriateness**.

A Tier 2 call may still require SPD presence for safety, legal, or scene-stabilization reasons. The classification is intended to identify where CARE could plausibly help, not to claim that CARE should automatically replace police in every case.

---

## Systemwide Results

### 1. Current Response Distribution

Across 324,386 unique 2025 incidents:

| Response Category    | Incidents |  Share |
| -------------------- | --------: | -----: |
| SPD only             |   317,556 | 97.89% |
| CARE only            |     3,847 |  1.19% |
| SPD/CARE co-response |     2,983 |  0.92% |

CARE-only and co-response together account for about 2.1% of all incidents.

---

### 2. Tiered CARE Classification

| CARE Tier                | Incidents | Share |
| ------------------------ | --------: | ----: |
| Tier 0 — Traditional SPD |   177,837 | 54.8% |
| Tier 1 — Potential CARE  |   125,682 | 38.7% |
| Tier 2 — Clearly CARE    |    20,867 |  6.4% |

Tier 2 represents the clearest segment of care-relevant calls.

---

## Response Alignment

Cross-tabulating response category by care tier shows that CARE is generally reaching the types of calls it is designed to address, but total CARE coverage remains limited.

| Response Category    |  Tier 0 |  Tier 1 | Tier 2 |   Total |
| -------------------- | ------: | ------: | -----: | ------: |
| CARE                 |     709 |      51 |  3,087 |   3,847 |
| SPD                  | 176,658 | 124,118 | 16,780 | 317,556 |
| SPD/CARE Co-Response |     470 |   1,513 |  1,000 |   2,983 |

Key observations:

* 80.2% of CARE-only responses land in Tier 2.
* 50.7% of co-response events occur in Tier 1, suggesting Tier 1 may function as an uncertainty or safety-bridge category.
* 80.4% of Tier 2 incidents are still handled SPD-only.
* CARE involvement in Tier 0 calls appears to be driven partly by officer-initiated or field-discovered needs, rather than simple dispatch misclassification.

Interpretation:

CARE appears directionally well-targeted, but the limited share of CARE involvement among Tier 2 calls suggests potential capacity, staffing, hours, geography, or dispatch-rule constraints.

---

## Neighborhood Equity Analysis

To understand whether CARE-eligible crisis burden aligns with neighborhood vulnerability, Tier 2 call rates were analyzed against Seattle’s RSE Composite Index.

### Tier 2 Rate by RSE Priority Category

| RSE Priority Category               | Tier 2 Rate per 1,000 Residents |
| ----------------------------------- | ------------------------------: |
| Lowest priority / highest advantage |                           22.81 |
| Second lowest priority              |                           29.87 |
| Middle priority                     |                           43.31 |
| Second highest priority             |                           34.48 |
| Highest priority / greatest need    |                           65.72 |

Key finding:

Neighborhoods in the highest RSE priority category experience a Tier 2 burden of 65.72 calls per 1,000 residents, nearly three times the rate of the lowest-priority neighborhoods.

The relationship between RSE percentile and Tier 2 call burden is moderately positive and statistically significant:

```text
Spearman rho = 0.40
p-value = 0.010
```

Interpretation:

Higher-vulnerability neighborhoods tend to experience higher Tier 2 crisis call burden, though the relationship is not perfectly monotonic.

---

## RSE Sub-Index Analysis

The analysis also compares Tier 2 call burden against the three RSE sub-indices.

| RSE Sub-Index              | Pearson r | Spearman rho |    R² |
| -------------------------- | --------: | -----------: | ----: |
| Socioeconomic disadvantage |     0.414 |        0.442 | 0.172 |
| Race/ELL/Origins           |     0.322 |        0.398 | 0.104 |
| Health disadvantage        |     0.316 |        0.317 | 0.100 |

Key finding:

Socioeconomic disadvantage is the strongest bivariate correlate of Tier 2 call burden.

However, even the strongest relationship explains only a modest share of neighborhood-level variation. Tier 2 concentration is also likely shaped by:

* Land use
* Service density
* Transit hubs
* Commercial activity
* Daytime population
* Reporting behavior
* Patrol visibility
* Neighborhood aggregation effects

---

## Destination Neighborhood Effect

Some neighborhoods have extremely high per-capita Tier 2 rates because they have low residential populations but high daytime, visitor, commercial, or service activity.

Examples include:

| Neighborhood             | Tier 2 Events | Population | Rate per 1,000 |
| ------------------------ | ------------: | ---------: | -------------: |
| Downtown Commercial Core |         1,448 |      5,813 |         249.10 |
| Georgetown               |           143 |      1,457 |          98.15 |
| Judkins Park             |           369 |      4,284 |          86.13 |
| Ballard                  |           963 |     11,191 |          86.05 |

This suggests a two-part deployment strategy:

1. **Equity-based placement:** prioritize high-RSE residential neighborhoods.
2. **Activity-based placement:** prioritize high-traffic commercial, industrial, and service-dense hubs.

---

## Neighborhood Strategy Matrix

The project compares each neighborhood on two dimensions:

* Tier 2 demand volume
* CARE involvement share among Tier 2 events

This creates a strategic matrix:

| Quadrant                      | Interpretation                           |
| ----------------------------- | ---------------------------------------- |
| High demand + high CARE share | Success hubs / capacity pressure         |
| High demand + low CARE share  | Service gaps / priority pilot candidates |
| Low demand + high CARE share  | Localized success patterns               |
| Low demand + low CARE share   | Lower immediate deployment priority      |

### Identified Service Gap Neighborhoods

Neighborhoods with relatively high Tier 2 volume and low CARE involvement include:

* First Hill
* Northgate/Maple Leaf
* Central Area/Squire Park
* Belltown
* Broadview/Bitter Lake
* Queen Anne
* Ravenna/Bryant
* Greenwood/Phinney Ridge

### First Hill as a Priority Example

First Hill stands out because it has:

* High Tier 2 volume
* High RSE vulnerability
* Low CARE involvement share
* Strong reliance on 911 calls rather than patrol-based discovery

This makes it a strong candidate for a targeted CARE capacity or placement pilot.

---

## Hourly Response Patterns

The analysis compares hourly CARE involvement between “success hubs” and “service gap” neighborhoods.

Main finding:

Both groups show very low CARE involvement overnight. The major divergence occurs during daytime and evening hours, especially between approximately 12:00 and 20:00.

During this window:

* Service gap neighborhoods show lower CARE involvement.
* Success hubs show substantially higher CARE involvement at similar hours.

Interpretation:

The gap may not be only about time of day. It may also reflect where units are based, how many units are available, and how local dispatch or field handoff pathways operate.

---

## Field Discovery and Equity

Approximately 21% of Tier 2 incidents are identified through officer-initiated ONVIEW activity rather than 911 dispatch.

This suggests that CARE access is partly field-discovered.

Interpretation:

* Some care needs are surfaced when officers encounter people in distress before a 911 call occurs.
* This may create a pre-crisis intervention window.
* It also raises an equity question: neighborhoods with lower patrol visibility or lower public-space visibility may have less access to field-discovered CARE support.

This analysis distinguishes between:

### Reactive Zones

High 911 reliance and low ONVIEW discovery.

Examples include:

* First Hill
* Lake City
* Roosevelt/Ravenna
* Bitter Lake
* Queen Anne
* Belltown

### Proactive Zones

Lower 911 reliance and higher ONVIEW discovery, often shaped by foot traffic, mixed-use land patterns, service density, or patrol visibility.

Key takeaway:

Scaling CARE equitably requires attention not only to dispatch rules, but also to field workflows, patrol-mediated discovery, geography, and visibility.

---

## Technical Methods

This project uses:

* Python
* Pandas
* DuckDB
* Geospatial/neighborhood joins
* Regular-expression-based call classification
* Population-weighted aggregation
* Rate calculations per 1,000 residents
* Correlation analysis
* Spearman and Pearson correlation
* RSE quintile analysis
* Neighborhood strategy segmentation
* Hourly response pattern analysis

---

## Suggested Repository Structure

```text
Seattle911/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   ├── 01_data_download_and_deduplication.ipynb
│   ├── 02_care_tier_classification.ipynb
│   ├── 03_rse_neighborhood_join.ipynb
│   ├── 04_equity_analysis.ipynb
│   └── 05_neighborhood_strategy_matrix.ipynb
├── src/
│   ├── preprocessing.py
│   ├── tier_classifier.py
│   ├── rse_join.py
│   └── analysis_utils.py
├── outputs/
│   ├── figures/
│   └── tables/
└── docs/
    └── methodology_notes.md
```

Large raw and processed datasets should remain local and are excluded from GitHub.

---

## How to Reproduce

1. Clone the repository.

```bash
git clone https://github.com/YOUR-USERNAME/Seattle911.git
cd Seattle911
```

2. Create a virtual environment.

```bash
python -m venv .venv
source .venv/bin/activate
```

3. Install dependencies.

```bash
pip install -r requirements.txt
```

4. Download public datasets from Seattle Open Data.

Required inputs:

* Seattle 911 CAD data, 2025
* Seattle Racial and Social Equity Composite Index
* Neighborhood / Community Reporting Area lookup or mapping file

5. Place downloaded files locally.

Suggested local-only structure:

```text
Data/
data_processed/
```

These folders are ignored by Git.

6. Run notebooks or scripts in order:

```text
01_data_download_and_deduplication
02_care_tier_classification
03_rse_neighborhood_join
04_equity_analysis
05_neighborhood_strategy_matrix
```

---

## Limitations


Important limitations:

1. **Eligibility is not appropriateness.**
   A call may be care-relevant but still require SPD presence due to safety, legal, or scene-stabilization needs.

2. **CAD data is operational, not diagnostic.**
   Dispatch categories reflect caller framing, dispatcher protocols, and routing logic. They do not fully capture underlying need.

3. **Regex classification is transparent but imperfect.**
   The tiering framework should be validated with dispatchers, CARE staff, SPD, and program experts.

4. **Neighborhood joins involve spatial approximation.**
   RSE data is census-tract based, while dispatch geography uses operational neighborhood categories. Population-weighted aggregation reduces but does not eliminate mismatch.

5. **CARE involvement does not capture all informal support.**
   Some incidents may include field-level support, backup, or referrals not fully captured in response category variables.

---

## Policy and Operational Implications

This analysis supports three practical directions:

### 1. Expand CARE where demand is high and involvement is low

Neighborhoods like First Hill and Northgate/Maple Leaf may be strong candidates for targeted pilots.

### 2. Treat Tier 1 as a co-response boundary space

Tier 1 is large, stable, and often handled by SPD. Co-response may be a practical model for selected Tier 1 call types.

### 3. Balance equity-based and activity-based placement

CARE expansion should account for both:

* Neighborhood vulnerability and residential need
* High-traffic destination hubs where crisis volume concentrates

---



This project is an independent public health data science analysis.



---

## Author

**Yeshi Dolma**
Applied Data Scientist transitioning into public health systems, health data engineering and health AI.

Focus areas:

* Public health data science
* Health equity
* Alternative crisis response
* ML/AI for health systems
* Policy-relevant analytics
* Data pipelines and measurement frameworks

---

## Disclaimer

This project is an independent analysis based on public data and is not an official analysis by the City of Seattle, SPD, or the CARE Department. Findings should be interpreted as exploratory and hypothesis-generating.
