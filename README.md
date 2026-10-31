# Bremen Transit: Understanding Recurring Delays

**Where delays occur, when they persist, and what BSAG can do**

Group project for **Digital Transformation and Innovation (MDSSB-DSAI-01)**, Constructor University, Bremen.

**Read the report:** the full report is published with GitHub Pages; the link is in the **About** box on this repository's main page.

## Project Overview

Public transport reliability is important for people who depend on buses and trams for commuting, education, and everyday activities.

This project investigates recurring delays in Bremen's public transport network using GTFS real-time data collected between **17 and 31 August 2026**.

Rather than focusing only on average delays, we examine where delays occur, whether they repeat across multiple days, and how differences in data coverage affect the interpretation of results.

The analysis is conducted using **R and Quarto**, combining data cleaning, exploratory analysis, statistical summaries, and visualizations.

## Research Question

**Where and when do recurring delays occur in Bremen's public transport network, and what can these patterns tell us about service reliability?**

We explore four related questions:

1. Which stops and routes are chronically delayed?
2. When do delays occur (time of day, weekday)?
3. What might explain them?
4. What could BSAG or the city do about it?

## Objectives

The project aims to:

- Identify recurring delay hotspots in Bremen.
- Compare delay patterns across stops and dates.
- Examine the frequency and magnitude of recorded delays.
- Distinguish recurring patterns from isolated observations.
- Evaluate limitations caused by missing or uneven data coverage.
- Provide evidence-informed recommendations for public transport monitoring.

## Data

### Data Source

The project uses **General Transit Feed Specification (GTFS) real-time data** for Bremen's public transport network, obtained via [GTFS.de](https://gtfs.de/de/realtime/).

**Observation period:** 17–31 August 2026

The dataset consists of 4,052 snapshots taken every five minutes. Each snapshot records `trip_id`, `stop_id`, `delay_seconds` (positive = late, negative = early) and `timestamp_fetched`.

A supplementary lookup file, `bremen_lookup.csv`, connects stop identifiers with stop names, coordinates, routes and headsigns.

### Data Preparation

Before analysis, the data is processed to improve consistency and reduce duplication.

The preparation steps are:

1. Importing all 4,052 GTFS real-time snapshots (978,735 rows).
2. Removing exact duplicate rows.
3. Removing implausible delays of more than one hour early or late, which are more likely feed errors than real delays.
4. Keeping one observation per trip × stop × date: the last snapshot, which is the closest available proxy for the delay when the vehicle passed the stop. This leaves **103,247 observations**.
5. Joining observations with stop information from the lookup file by `stop_id`.
6. Checking missing values, unmatched stops and daily data coverage.

These steps help ensure that repeated snapshots are not automatically interpreted as independent delay events.

## Methodology

### 1. Exploratory Data Analysis

We examine the distribution of recorded delays to understand the overall patterns in the dataset. A delay of **5 minutes or more** is treated as a significant delay.

### 2. Stop-Level Analysis

Delay observations are grouped by stop to identify locations where delays appear repeatedly.

- **Ranking:** stops are ranked by the lower bound of a 95% Wilson confidence interval for the share of significant delays, and only if they have at least 200 observations. This prevents stops with few trips from topping the list by chance.
- **Persistence:** for each stop we count the days on which at least 20% of trips were 5 or more minutes late, using only days with at least 20 trips and stops observed on at least five days.
- **Sensitivity checks:** the rankings are recalculated with alternative thresholds to test how much they depend on these choices.

Particular attention is given to **Stellfeldsweg (line 61)**, which is examined as a case study.

### 3. Temporal Analysis

We investigate how delay patterns change by hour of day, weekday and date across the observation period.

### 4. Data Coverage Assessment

Real-time observations are not necessarily available equally across all stops, dates, or time periods.

We therefore examine coverage alongside delay patterns.

A stop with more recorded observations may appear more frequently in the data without necessarily experiencing worse service.

### 5. Interpretation

Results are interpreted descriptively.

The analysis identifies observed patterns but does not establish the causes of delays. Possible causes are treated as hypotheses to be checked against independent evidence.

## Main Findings

### Delay Patterns Are Uneven

- Of the 103,247 observations, **11.4%** were 5 or more minutes late, and the median delay was **1.3 minutes**.
- The daily share of significant delays ranged from **5.7%** (Sunday 23 August) to **15.6%** (Thursday 20 August).

### Recurring Hotspots Deserve Attention

- The stops with the highest share of significant delays were **Dohlenstraße** (50.7% of 576 observations), **Stellfeldsweg** (41.9% of 3,061) and **Roland-Center** (43.5% of 301).
- The most persistent stops were **Dohlenstraße** (a high-delay day on all 8 days observed), **Berufsbildungswerk** (7 of 7 days) and **Kulenkampffallee** (7 of 10 days). These are the report's priority stops.
- Stellfeldsweg had high-delay days on 6 of the 7 days it was observed.
- In the afternoon peak (15:00–17:59), 58.3% of observations at Dohlenstraße and 43.0% at Stellfeldsweg were 5 or more minutes late.

### Data Coverage Matters

- Recorded observations at Stellfeldsweg fell from about **680 per day** (18–21 August) to **25 or fewer per day** from 23 August, while observations across the rest of the network increased.
- On weekdays, the share of significant delays there dropped from 44.7% to 5.7%, but the later figure rests on only **88 observations**. This is more consistent with a gap in data coverage than with firm evidence of improved service.

### Monitoring Can Support Better Decisions

Repeated real-time observations can help identify locations that deserve further operational investigation.

Additional timetable, route, and operational data would be needed to explain why particular delays occur.

## Tools and Technologies

- **R** (tidyverse, lubridate, scales, knitr): data cleaning, analysis, and visualization.
- **Quarto**: reproducible analytical reporting.
- **Positron**: development environment (RStudio works as well).
- **GitHub**: version control, group collaboration and publishing the report with GitHub Pages.
- **GTFS real-time data**: public transport observations.

## Project Structure

```text
Repository
├── README.md             this file
├── index.html            rendered report
├── Bremen_Transit.qmd    Quarto source with all analysis code and text
├── bremen_lookup.csv     stop and route lookup
└── .gitignore
```

The raw snapshots (`update_data/`, 4,052 CSV files) are not included in the repository because of their size. The `data_processed/` folder is a cache that is created automatically when the report is rendered.

## Reproducing the Analysis

### Requirements

- R (the report was produced with R 4.6.1)
- Quarto (the report was produced with Quarto 1.10)
- Positron, RStudio, or another R-compatible development environment
- The R packages used by the report:

```r
install.packages(c("tidyverse", "lubridate", "scales", "knitr", "rmarkdown"))
```

### Instructions

1. Clone or download this repository.
2. Add the GTFS real-time snapshots in a folder called `update_data/` next to `Bremen_Transit.qmd`:

   ```text
   Bremen_Transit.qmd
   bremen_lookup.csv
   update_data/
       2026_08_17T22_10.csv
       2026_08_17T22_15.csv
       ...
   ```

3. Open the folder in Positron or RStudio.
4. Render the report from that folder:

```bash
quarto render Bremen_Transit.qmd
```

The first render reads every snapshot and takes a few minutes. The processed data is then cached in `data_processed/prep.rds`, so later renders are quick.

## Limitations

Several limitations should be considered when interpreting the results.

### Observation Window

The dataset covers approximately two weeks in August 2026.

Patterns observed during this period may not represent longer-term service performance.

### Uneven Data Coverage

The number of observations differs across stops and dates.

Missing observations do not necessarily mean that services operated without delays.

### Snapshot-Based Measurements

GTFS real-time snapshots provide delay estimates at collection times rather than independently measured arrival times.

### Timestamp Interpretation

Whether the feed's timestamps are stored in local time or UTC has not been independently verified, so hour-of-day results should be treated as provisional.

### Route Attribution

Many stops are served by several lines, so route rankings use only stops served by a single line. The optional route-inference analysis was not run, so no inferred-route results are reported.

### Causality

The analysis cannot determine whether delays were caused by congestion, construction, scheduling, vehicle operations, or other factors without additional evidence.

## Recommendations

Based on the findings, we recommend that BSAG:

1. **Review priority stops:** conduct targeted operational reviews of Dohlenstraße, Berufsbildungswerk, Kulenkampffallee and Stellfeldsweg, including scheduled running times, stopping conditions and traffic interactions.
2. **Monitor recurring delays:** track delays by stop, hour and day in a regular performance dashboard, prioritizing stops with enough observations and persistent delays.
3. **Check data coverage:** investigate why observations at Stellfeldsweg dropped after 22 August and set up automated coverage checks, so that missing data is not mistaken for improved punctuality.

These are recommendations for further monitoring and investigation, not confirmed explanations for the observed delays.

## Project Team

This project was developed by:

- Shaleen Debeila
- Lutendo Boitumelo Mulea
- Rebeccah Chepkemoi Kapsekei
- Limpo Shawa
- Desree Mmaphuti Matlala

## Project Report

The full Quarto report contains the detailed data preparation, analysis, tables, visualizations, discussion, and limitations.

It is published with GitHub Pages (link in the **About** box) and is also included as `index.html`, which can be downloaded and opened in any browser.

## Conclusion

Understanding public transport reliability requires more than identifying where large delays are recorded.

By examining recurring patterns alongside data coverage, this project demonstrates how GTFS real-time data can support a more careful assessment of Bremen's bus and tram network.

The findings provide a starting point for identifying potential reliability problems and developing more targeted operational investigations.
