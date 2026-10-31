# Bremen Transit: Understanding Recurring Delays

*Where delays occur, when they persist, and what BSAG can do*

Group project for **Digital Transformation and Innovation (MDSSB-DSAI-01)**, Constructor University, Bremen.

**Authors:** Shaleen Debeila, Lutendo Boitumelo Mulea, Rebeccah Chepkemoi Kapsekei, Limpo Shawa, Desree Mmaphuti Matlala

## Read the report

The full report is published with GitHub Pages; the link is in the **About** box on this repository's main page. You can also download `index.html` and open it in any browser: it is self-contained, with all charts and code included.

## What the study does

We use GTFS real-time delay snapshots for Bremen's bus and tram network, collected every five minutes between 17 and 31 August 2026, to answer four questions:

1. Which stops and routes are chronically delayed?
2. When do delays occur (time of day, weekday)?
3. What might explain them?
4. What could BSAG or the city do about it?

Stops are ranked by the lower bound of a 95% Wilson confidence interval for the share of observations 5 or more minutes late, and checked for persistence across days. Stellfeldsweg (line 61) is examined as a case study.

## Repository contents

| File | Description |
|---|---|
| `index.html` | Rendered report |
| `Bremen_Transit.qmd` | Quarto source with all analysis code and text |
| `bremen_lookup.csv` | Stop and route lookup: stop IDs, names, coordinates, routes and headsigns |
| `README.md` | This file |

The raw snapshot files (`update_data/`, about 4,000 CSV files) are not included because of their size. Each snapshot has the columns `trip_id`, `stop_id`, `delay_seconds` and `timestamp_fetched`.

## Reproducing the report

1. Install [R](https://www.r-project.org/) and [Quarto](https://quarto.org/), then the R packages:

   ```r
   install.packages(c("tidyverse", "lubridate", "scales", "knitr", "rmarkdown"))
   ```

2. Put the files in one folder, with the snapshot CSVs in `update_data/`:

   ```
   Bremen_Transit.qmd
   bremen_lookup.csv
   update_data/
       2026_08_17T22_10.csv
       2026_08_17T22_15.csv
       ...
   ```

3. Render from that folder:

   ```
   quarto render Bremen_Transit.qmd
   ```

The first render reads every snapshot and takes a few minutes. The processed data is then cached in `data_processed/prep.rds`, so later renders are quick. The report was produced with R 4.6.1 and Quarto 1.10.

## Data source

GTFS real-time feed for Bremen via [GTFS.de](https://gtfs.de/de/realtime/).
