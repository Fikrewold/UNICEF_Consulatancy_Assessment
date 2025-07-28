---
title: "UNICEF Consultancy Assessment"
subtitle: "Maternal Health Coverage Analysis"
author: "UNICEF Data and Analytics Technical Evaluation"
date: "2025-07-28"
output: 
  github_document:
    toc: true
    toc_depth: 2
---

# UNICEF Consultancy Assessment: Maternal Health Coverage Analysis

## Overview

This repository contains the analysis for the **UNICEF Data and Analytics technical evaluation**, focusing on maternal health coverage disparities between countries that are on-track versus off-track for achieving under-5 mortality rate (U5MR) targets.

### Research Focus

The analysis examines population-weighted coverage rates for two critical maternal health indicators:
- **ANC4**: Antenatal care 4+ visits - percentage of women (aged 15-49 years) who attended at least four times during pregnancy by any provider
- **SBA**: Skilled birth attendant - percentage of deliveries attended by skilled health personnel

## Key Findings

The population-weighted analysis reveals significant disparities in maternal health service access:

| Indicator | On-track Countries | Off-track Countries | Coverage Gap |
|-----------|:------------------:|:-------------------:|:------------:|
| **ANC4**  | 72.8%             | 55.4%              | 17.4 pp      |
| **SBA**   | 92.5%             | 68.8%              | 23.7 pp      |

> **Key Insight**: On-track countries consistently outperform off-track countries across both indicators, with skilled birth attendance showing the largest disparity (23.7 percentage points).

## Data Sources

| Dataset | Description | Time Period |
|---------|-------------|-------------|
| **UNICEF Global Data Flow** | Maternal health coverage indicators | 2018-2022 |
| **Track Status Classification** | Country classifications based on U5MR progress | Current |
| **UN World Population Prospects 2022** | Demographic indicators and birth projections | 2022 |

## Repository Structure

```
UNICEF_Consultancy_Assessment/
├── README.md                                       # Repository documentation
├── README.Rmd                                      # R Markdown source file
├── Consultancy_Assessment.Rmd                      # Main analysis script
├── Consultancy_Assessment.pdf                      # Final analysis report
├── UNICEF_ConsulatancyAssessment.Rproj            # R Project file
│
├── data/
│   ├── GLOBAL_DATAFLOW_2018-2022.xlsx            # UNICEF coverage data
│   ├── On-track and off-track countries.xlsx      # Country track status
│   ├── WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS...  # UN demographic data
│   ├── anc4_most_recent.xlsx                      # Processed ANC4 data
│   └── sbc_most_recent.xlsx                       # Processed SBA data
│
└── outputs/
    └── coverage_visualization.png                  # Analysis charts
```

## Methodology

### Step 1: Data Preparation
- Import and harmonize datasets using consistent ISO3 country codes
- Filter UN World Population Prospects for 2022 country-level data only
- Map country names to ISO3 codes using the `countrycode` package
- Extract most recent coverage data (2018-2022) for ANC4 and SBA indicators

### Step 2: Population-Weighted Analysis
- Calculate population-weighted coverage by track status using projected 2022 births as weights
- Group countries as "On-track" (Achieved/On Track) vs "Off-track" based on U5MR status
- Apply weighting formula: **Σ(coverage × births) / Σ(births)** for each group

### Step 3: Visualization and Reporting
- Create comparative visualizations using `ggplot2`
- Generate summary statistics and interpretation

## Installation & Setup

### Prerequisites
- R (version 4.0 or higher)
- RStudio (recommended)

### Required R Packages

```r
# Install required packages
install.packages(c(
  "readxl",      # Excel file reading
  "readr",       # Data import
  "dplyr",       # Data manipulation
  "ggplot2",     # Visualization
  "countrycode", # Country code mapping
  "fuzzyjoin",   # Fuzzy joining
  "scales",      # Scale formatting
  "ggtext"       # Rich text formatting
))

# Optional for PDF generation
install.packages("tinytex")
tinytex::install_tinytex()
```

### Load Libraries

```r
library(readxl)
library(readr)
library(dplyr)
library(ggplot2)
library(countrycode)
library(fuzzyjoin)
library(scales)
library(ggtext)
```

## Usage

### 1. Clone Repository

```bash
git clone https://github.com/Fikrewold/UNICEF_Consultancy_Assessment.git
cd UNICEF_Consultancy_Assessment
```

### 2. Open R Project

```r
# Open UNICEF_ConsulatancyAssessment.Rproj in RStudio
```

### 3. Run Analysis

```r
# Render the main analysis
rmarkdown::render("Consultancy_Assessment.Rmd")

# Or render this README
rmarkdown::render("README.Rmd")
```

## Analysis Workflow

### Data Import and Cleaning

```r
# Load UNICEF data
gdf <- read_excel("GLOBAL_DATAFLOW_2018-2022.xlsx", sheet = "Unicef data")

# Load track status data
track <- read_excel("On-track and off-track countries.xlsx", sheet = "Sheet1")

# Load UN population data (skip first 16 rows)
projections <- read_excel("WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS_COMPACT_REV1.xlsx", 
                         sheet = "Projections", skip = 16)
```

### Population-Weighted Calculation

```r
# Function to calculate weighted average
calculate_weighted_average <- function(data, value_col, weight_col) {
  sum(data[[value_col]] * data[[weight_col]]) / sum(data[[weight_col]])
}

# Example for ANC4
anc4_clean <- anc4 %>%
  filter(!is.na(OBS_VALUE), !is.na(Births2022), !is.na(Status.U5MR)) %>%
  mutate(
    OBS_VALUE = as.numeric(OBS_VALUE),
    ANC4_prop = OBS_VALUE / 100,
    Track = ifelse(Status.U5MR %in% c("Achieved", "On Track"), "On-track", "Off-track")
  )
```

### Results Summary

**Population-weighted ANC4 by Track:**
- Off-track: **55.4%**
- On-track: **72.8%**

**Population-weighted SBA by Track:**
- Off-track: **68.8%**
- On-track: **92.5%**

## Key Features

- ✅ **Data Integration**: Seamless merging of multiple international datasets
- ✅ **Country Code Harmonization**: Robust mapping system for consistent identification
- ✅ **Population Weighting**: Accounts for country population sizes in calculations
- ✅ **Custom Regional Codes**: Handles regional/organizational entities
- ✅ **Reproducible Analysis**: Complete R Markdown workflow
- ✅ **Professional Visualization**: Publication-ready charts with `ggplot2`

## Technical Specifications

### Data Processing Notes
- **Coverage Period**: Most recent data available per country (2018-2022)
- **Population Weights**: UN World Population Prospects 2022 birth projections
- **Country Classification**: Based on U5MR progress status
- **Missing Data Handling**: Countries with incomplete data excluded from calculations

### Custom ISO3 Codes for Regional Entities

```r
custom_codes <- c(
  "African Union" = "AFU",
  "Americas" = "AME", 
  "Arab States" = "ARB",
  "Asia and the Pacific" = "ASP",
  "World Bank (high income)" = "WBH",
  "World Bank (low income)" = "WBL"
  # ... additional regional codes
)
```

## Limitations

- **Subnational Variation**: Does not capture within-country disparities
- **Data Consistency**: Assumes uniform reporting standards across countries
- **Temporal Coverage**: Limited to available data timeframe (2018-2022)
- **Causal Inference**: Analysis shows associations, not causal relationships

## Visualization Output

The analysis generates a comparative bar chart showing population-weighted coverage rates by track status, highlighting maternal health service gaps between country groups.

**Chart Features:**
- Color-coded by track status (On-track: green, Off-track: orange)
- Percentage labels on bars
- Professional styling with `theme_minimal()`
- Publication-ready quality

## Contributing

This repository is part of a technical evaluation. For questions or clarifications:

1. Review the analysis methodology in `Consultancy_Assessment.Rmd`
2. Check data sources and processing steps
3. Examine visualization code and output

## License

This analysis is prepared for UNICEF Data and Analytics technical evaluation purposes.

## Contact Information

**UNICEF Data and Analytics Education Team Technical Evaluation**

Repository maintains tasks for the UNICEF consultancy assessment focusing on maternal health coverage analysis and population-weighted statistical methods.

---

**Analysis Date:** July 28, 2025  
**Data Sources:** UNICEF Global Data Flow, UN World Population Prospects 2022  
**Analysis Tool:** R Statistical Software with R Markdown  
**Repository:** https://github.com/Fikrewold/UNICEF_Consultancy_Assessment