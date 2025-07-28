---
title: "UNICEF Consultancy Assessment: Maternal Health Coverage Analysis"
author: "UNICEF Data and Analytics Technical Evaluation"
date: "`r Sys.Date()`"
output: 
  github_document:
    toc: true
    toc_depth: 3
  html_document:
    toc: true
    toc_float: true
    theme: flatly
    highlight: tango
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(
  echo = TRUE,
  warning = FALSE,
  message = FALSE,
  fig.width = 10,
  fig.height = 6,
  dpi = 300
)
```

## Overview

This repository contains the analysis for the UNICEF Data and Analytics technical evaluation, focusing on **maternal health coverage disparities** between countries that are on-track versus off-track for achieving under-5 mortality rate (U5MR) targets.

The analysis examines population-weighted coverage rates for:

- **ANC4**: Antenatal care 4+ visits (percentage of women aged 15-49 years who attended at least four times during pregnancy)
- **SBA**: Skilled birth attendant coverage (percentage of deliveries attended by skilled health personnel)

## Key Findings

The population-weighted analysis reveals significant disparities in maternal health service access:

```{r results-table, echo=FALSE}
# Create results summary table
results_data <- data.frame(
  Indicator = c("ANC4", "SBA"),
  `On-track Countries` = c("72.8%", "92.5%"),
  `Off-track Countries` = c("55.4%", "68.8%"),
  Gap = c("17.4 pp", "23.7 pp"),
  check.names = FALSE
)

knitr::kable(results_data, 
             caption = "Population-weighted Coverage Rates by Track Status",
             align = c("l", "c", "c", "c"))
```

These findings highlight persistent gaps in maternal health service access, with on-track countries consistently outperforming off-track countries across both indicators.

## Repository Structure

```
├── README.md                                    # This file
├── README.Rmd                                   # R Markdown source
├── Consultancy_Assessment.Rmd                   # R Markdown analysis script
├── Consultancy_Assessment.pdf                   # Final analysis report
├── UNICEF_ConsulatancyAssessment.Rproj         # R Project file
├── GLOBAL_DATAFLOW_2018-2022.xlsx             # UNICEF health coverage data
├── On-track and off-track countries.xlsx       # Country track status data
├── WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS...   # UN World Population Prospects data
├── anc4_most_recent.xlsx                       # Processed ANC4 data
└── sbc_most_recent.xlsx                        # Processed SBA data
```

## Data Sources

1. **UNICEF Global Data Flow (2018-2022)**: Maternal health coverage indicators
2. **On-track and Off-track Countries**: Country classifications based on U5MR progress
3. **UN World Population Prospects 2022**: Demographic indicators and birth projections

## Methodology

### Step 1: Data Preparation

- Import and harmonize datasets using consistent ISO3 country codes
- Filter UN World Population Prospects for 2022 country-level data
- Map country names to ISO3 codes using the `countrycode` package
- Extract most recent coverage data (2018-2022) for ANC4 and SBA indicators

### Step 2: Population-Weighted Analysis

- Calculate population-weighted coverage by track status using projected 2022 births as weights
- Group countries as "On-track" (Achieved/On Track) vs "Off-track" based on U5MR status
- Apply weighting formula: `Σ(coverage × births) / Σ(births)` for each group

### Step 3: Visualization and Reporting

- Create comparative visualizations using `ggplot2`
- Generate summary statistics and interpretation

## Requirements

### R Packages

```{r packages, eval=FALSE}
# Core packages
library(readxl)      # Excel file reading
library(readr)       # Data import
library(dplyr)       # Data manipulation
library(ggplot2)     # Visualization
library(countrycode) # Country code mapping
library(fuzzyjoin)   # Fuzzy joining
library(scales)      # Scale formatting
library(ggtext)      # Rich text formatting

# Optional for PDF generation
library(tinytex)
```

### Installation

```{r installation, eval=FALSE}
# Install required packages
install.packages(c("readxl", "readr", "dplyr", "ggplot2", 
                   "countrycode", "fuzzyjoin", "scales", "ggtext"))

# For PDF generation (optional)
install.packages("tinytex")
tinytex::install_tinytex()
```

## Usage

### 1. Clone the repository

```bash
git clone https://github.com/Fikrewold/UNICEF_Consultancy_Assessment.git
cd UNICEF_Consultancy_Assessment
```

### 2. Open the R Project

```{r project-setup, eval=FALSE}
# Open UNICEF_ConsulatancyAssessment.Rproj in RStudio
```

### 3. Run the analysis

```{r run-analysis, eval=FALSE}
# Execute the R Markdown file
rmarkdown::render("Consultancy_Assessment.Rmd")

# Or render this README
rmarkdown::render("README.Rmd")
```

## Key Features

- **Data Integration**: Seamless merging of multiple international datasets
- **Country Code Harmonization**: Robust mapping system for consistent country identification
- **Population Weighting**: Accounts for country population sizes in coverage calculations
- **Custom Regional Codes**: Handles regional/organizational entities beyond standard country codes
- **Reproducible Analysis**: Complete R Markdown workflow from data import to visualization

## Sample Analysis Code

```{r sample-analysis, eval=FALSE}
# Example of population-weighted calculation
calculate_weighted_average <- function(data, value_col, weight_col) {
  sum(data[[value_col]] * data[[weight_col]]) / sum(data[[weight_col]])
}

# Filter and prepare ANC4 data
anc4_clean <- anc4 %>%
  filter(!is.na(OBS_VALUE), !is.na(Births2022), !is.na(Status.U5MR)) %>%
  mutate(
    OBS_VALUE = as.numeric(OBS_VALUE),
    ANC4_prop = OBS_VALUE / 100,
    Track = ifelse(Status.U5MR %in% c("Achieved", "On Track"), "On-track", "Off-track")
  )

# Calculate weighted averages by track status
for (grp in unique(anc4_clean$Track)) {
  sub_data <- anc4_clean %>% filter(Track == grp)
  weighted_ANC4 <- calculate_weighted_average(sub_data, "ANC4_prop", "Births2022")
  cat(" ", grp, ":", round(weighted_ANC4 * 100, 1), "%\n")
}
```

## Technical Notes

- **Data Quality**: Analysis assumes consistent data quality across countries and time periods
- **Coverage Period**: Uses most recent data available for each country (2018-2022)
- **Population Weights**: Based on UN World Population Prospects 2022 birth projections
- **Missing Data**: Countries with incomplete coverage or demographic data are excluded from calculations

## Limitations

- Does not capture subnational disparities within countries
- Assumes reporting consistency across different national statistical systems
- Limited to available data timeframe (may not reflect most current conditions)
- Regional/organizational entities use custom ISO3 codes for analysis purposes

## Visualization

The analysis produces a comparative bar chart showing population-weighted coverage rates by track status, clearly illustrating the maternal health service gaps between on-track and off-track countries.

```{r visualization-example, eval=FALSE}
# Example visualization code
ggplot(coverage_summary, aes(x = Indicator, y = Coverage * 100, fill = Track)) +
  geom_bar(stat = "identity", position = position_dodge(width = 0.9)) +
  geom_text(aes(label = paste0(round(Coverage * 100, 0), "%")),
            position = position_dodge(width = 0.9),
            vjust = -0.3, size = 4) +
  labs(
    title = "Population-Weighted Coverage by Track Status",
    subtitle = "A Comparison of On-Track and Off-Track Countries",
    y = "Coverage (%)",
    x = "",
    fill = "Status"
  ) +
  scale_y_continuous(breaks = seq(0, 100, 25), limits = c(0, 100)) +
  scale_fill_manual(values = c("On-track" = "#1b9e77", "Off-track" = "#d95f02")) +
  theme_minimal(base_size = 14)
```

## Session Information

```{r session-info, eval=FALSE}
# To display session information when knitting
sessionInfo()
```

## Contact

**Test for Consultancy with the D&A Education Team**

This repository contains the tasks for the UNICEF Data and Analytics technical evaluation for education.

---

*Analysis Date: `r format(Sys.Date(), "%B %d, %Y")`*  
*Data Sources: UNICEF, UN World Population Prospects 2022*  
*Generated with R Markdown*