
# UNICEF Consultancy Assessment: Maternal Health Coverage Analysis

**UNICEF Data and Analytics Technical Evaluation 2025-07-28**

> **Application Reference**: This is a solution for the positions applied for:
> - **Household Survey Data Analyst Consultant** – Req. #581656
> - **Microdata Harmonization Consultant** – Req. #581699

## Table of Contents

- [Overview](#overview)
- [Key Findings](#key-findings)
- [Data Sources](#data-sources)
- [Repository Structure](#repository-structure)
- [Methodology](#methodology)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Analysis Workflow](#analysis-workflow)
- [Key Features](#key-features)
- [Technical Specifications](#technical-specifications)
- [Limitations](#limitations)
- [Visualization Output](#visualization-output)
- [Contributing](#contributing)
- [License](#license)
- [Contact Information](#contact-information)

---

## Overview

This repository contains the analysis for the **UNICEF Data and Analytics technical evaluation**, focusing on maternal health coverage disparities between countries that are on-track versus off-track for achieving under-5 mortality rate (U5MR) targets.

### Research Focus

The analysis examines population-weighted coverage rates for two critical maternal health indicators:

- **ANC4**: Antenatal care 4+ visits - percentage of women (aged 15-49 years) who attended at least four times during pregnancy by any provider
- **SBA**: Skilled birth attendant - percentage of deliveries attended by skilled health personnel

The study uses projected 2022 births as weights to provide a more population-representative view of global maternal health service coverage.

---

## Key Findings

The population-weighted analysis reveals significant disparities in maternal health service access between country groups:

| Indicator | On-track Countries | Off-track Countries | Coverage Gap |
|-----------|:------------------:|:-------------------:|:------------:|
| **ANC4**  | 72.8%             | 55.4%              | 17.4 pp      |
| **SBA**   | 92.5%             | 68.8%              | 23.7 pp      |

> **Key Insight**: On-track countries consistently outperform off-track countries across both indicators, with skilled birth attendance showing the largest disparity (23.7 percentage points). These findings highlight persistent gaps in maternal health service access between the two groups.

### Summary of Results

**Population-weighted ANC4 Coverage by Track Status:**
- **Off-track countries**: 55.4%
- **On-track countries**: 72.8%
- **Difference**: 17.4 percentage points

**Population-weighted SBA Coverage by Track Status:**
- **Off-track countries**: 68.8%
- **On-track countries**: 92.5%
- **Difference**: 23.7 percentage points

---

## Data Sources

The analysis integrates three primary datasets to ensure comprehensive coverage and accurate population weighting:

| Dataset | Description | Time Period | Purpose |
|---------|-------------|-------------|---------|
| **UNICEF Global Data Flow** | Maternal health coverage indicators from UNICEF monitoring systems | 2018-2022 | ANC4 and SBA coverage rates |
| **Track Status Classification** | Country classifications based on U5MR progress toward SDG targets | Current | Grouping countries by development track |
| **UN World Population Prospects 2022** | Demographic indicators and birth projections | 2022 | Population weights for analysis |

### Data Quality Notes

- **Most Recent Data**: Analysis uses the most recent available data for each country within the 2018-2022 period
- **Population Weights**: Based on UN World Population Prospects 2022 birth projections
- **Country Coverage**: Includes all countries with complete data for coverage indicators and demographic weights

---

## Repository Structure

```
UNICEF_Consultancy_Assessment/
├── README.md                                       # Repository documentation
├── README.Rmd                                      # R Markdown source file
├── Consultancy_Assessment.Rmd                      # Main analysis script
├── Consultancy_Assessment.pdf                      # Final analysis report
├── UNICEF_ConsulatancyAssessment.Rproj            # R Project file
├── .gitignore                                      # Git ignore file
│
├── data/
│   ├── GLOBAL_DATAFLOW_2018-2022.xlsx            # UNICEF coverage data
│   ├── On-track and off-track countries.xlsx      # Country track status
│   ├── WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS...  # UN demographic data
│   ├── anc4_most_recent.xlsx                      # Processed ANC4 data
│   └── sbc_most_recent.xlsx                       # Processed SBA data
│
├── outputs/
│   ├── coverage_visualization.png                 # Main analysis chart
│   └── summary_tables.xlsx                        # Summary statistics
│
└── scripts/
    ├── data_preparation.R                         # Data cleaning functions
    ├── analysis_functions.R                       # Custom analysis functions
    └── visualization.R                             # Chart generation code
```

---

## Methodology

The analysis follows a systematic three-step approach to ensure robust and reproducible results:

### Step 1: Data Preparation

**Objective**: Import and harmonize datasets from all three sources, ensuring consistent country identifiers.

**Key Steps**:
- Import UNICEF Global Data Flow (2018-2022) maternal health indicators
- Load country track status classifications based on U5MR progress
- Import UN World Population Prospects 2022 data (skip first 16 rows for correct formatting)
- Map country names to ISO3 codes using the `countrycode` package
- Filter for 2022 country-level data only (Type="Country/Area")
- Extract most recent coverage data (2018-2022) for ANC4 and SBA indicators per country

**Data Processing**:
```r
# Filter UN projections for 2022
projections_2022 <- Projections %>%
  filter(Year == 2022, Type == "Country/Area")

# Extract most recent ANC4 data per country
anc4_most_recent <- gdf %>%
  filter(Indicator == "Antenatal care 4+ visits...") %>%
  group_by(Country) %>%
  filter(TIME_PERIOD == max(TIME_PERIOD, na.rm = TRUE)) %>%
  ungroup()
```

### Step 2: Population-Weighted Analysis

**Objective**: Compute population-weighted coverage for ANC4 and SBA indicators by track status using projected 2022 births as weights.

**Key Steps**:
- Group countries as "On-track" (Achieved/On Track) vs "Off-track" based on U5MR status
- Calculate weighted averages using the formula: **Σ(coverage × births) / Σ(births)** for each group
- Apply population weights to account for country size differences
- Generate summary statistics for both indicators by track status

**Weighting Formula**:
```r
calculate_weighted_average <- function(data, value_col, weight_col) {
  sum(data[[value_col]] * data[[weight_col]]) / sum(data[[weight_col]])
}
```

### Step 3: Visualization and Reporting

**Objective**: Create clear visualizations and comprehensive reporting of findings.

**Key Steps**:
- Generate comparative bar charts using `ggplot2`
- Create summary tables with key statistics
- Develop professional visualizations with proper styling
- Generate interpretive text and policy implications

---

## Installation & Setup

### Prerequisites

- **R**: Version 4.0 or higher
- **RStudio**: Recommended for best experience
- **Git**: For repository cloning

### Required R Packages

Install all required packages before running the analysis:

```r
# Core data manipulation and analysis packages
install.packages(c(
  "readxl",      # Excel file reading (.xlsx files)
  "readr",       # Enhanced data import capabilities
  "dplyr",       # Data manipulation and transformation
  "ggplot2",     # Advanced data visualization
  "countrycode", # Country code mapping and conversion
  "fuzzyjoin",   # Flexible joining operations
  "scales",      # Scale formatting for plots
  "ggtext"       # Rich text formatting in plots
))

# Optional packages for enhanced functionality
install.packages(c(
  "tinytex",     # LaTeX distribution for PDF generation
  "knitr",       # Dynamic report generation
  "rmarkdown"    # R Markdown document processing
))

# Install TinyTeX for PDF generation (optional)
tinytex::install_tinytex()
```

### Load Required Libraries

```r
# Load core libraries
library(readxl)
library(readr)
library(dplyr)
library(ggplot2)
library(countrycode)
library(fuzzyjoin)
library(scales)
library(ggtext)

# Optional libraries
library(knitr)
library(rmarkdown)
```

---

## Usage

### 1. Repository Setup

Clone the repository and set up your local environment:

```bash
# Clone the repository
git clone https://github.com/Fikrewold/UNICEF_Consultancy_Assessment.git

# Navigate to project directory
cd UNICEF_Consultancy_Assessment

# Open in RStudio (optional)
open UNICEF_ConsulatancyAssessment.Rproj
```

### 2. Project Environment

```r
# Open the R Project file in RStudio
# This ensures proper working directory and package management

# Verify working directory
getwd()

# Check available data files
list.files(".", pattern = "*.xlsx")
```

### 3. Run the Analysis

Execute the complete analysis workflow:

```r
# Option 1: Render the complete analysis
rmarkdown::render("Consultancy_Assessment.Rmd")

# Option 2: Render this README with updated results
rmarkdown::render("README.Rmd")

# Option 3: Run analysis interactively
source("Consultancy_Assessment.Rmd")
```

### 4. Generate Outputs

```r
# Generate PDF report
rmarkdown::render("Consultancy_Assessment.Rmd", output_format = "pdf_document")

# Generate HTML report
rmarkdown::render("Consultancy_Assessment.Rmd", output_format = "html_document")
```

---

## Analysis Workflow

### Data Import and Cleaning

```r
# Load UNICEF maternal health data
gdf <- read_excel("GLOBAL_DATAFLOW_2018-2022.xlsx", sheet = "Unicef data")

# Load country track status classifications
track <- read_excel("On-track and off-track countries.xlsx", sheet = "Sheet1")

# Load UN population projections (skip header rows)
col_names <- names(read_excel("WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS_COMPACT_REV1.xlsx", 
                             sheet = "Estimates"))
col_types <- rep("guess", length(col_names))
col_types[which(col_names == "ISO3 Alpha-code")] <- "text"
col_types[which(col_names == "ISO2 Alpha-code")] <- "text"

projections <- read_excel("WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS_COMPACT_REV1.xlsx", 
                         sheet = "Projections", 
                         col_types = col_types)
```

### Country Code Harmonization

```r
# Clean geographic area names
gdf <- gdf %>%
  mutate(Country = gsub("^\\(.*?\\)\\s*", "", `Geographic area`))

# Map country names to ISO3 codes
gdf <- gdf %>%
  mutate(`ISO3 Alpha-code` = countrycode(Country, 
                                        origin = "country.name", 
                                        destination = "iso3c"))

# Handle custom regional codes
custom_codes <- c(
  "African Union" = "AFU",
  "Americas" = "AME",
  "Arab States" = "ARB",
  "Asia and the Pacific" = "ASP",
  "Caribbean" = "CAR",
  "World Bank (high income)" = "WBH",
  "World Bank (low income)" = "WBL"
)

gdf <- gdf %>%
  mutate(`ISO3 Alpha-code` = ifelse(is.na(`ISO3 Alpha-code`),
                                   custom_codes[Country],
                                   `ISO3 Alpha-code`))
```

### Population-Weighted Calculation

```r
# Prepare ANC4 data with track grouping
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

### Visualization Generation

```r
# Create summary data for plotting
coverage_summary <- bind_rows(anc4_summary, sbc_summary)

# Generate comparative bar chart
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

---

## Key Features

The analysis framework provides several advanced capabilities:

- ✅ **Multi-Source Data Integration**: Seamless merging of UNICEF, UN, and classification datasets
- ✅ **Robust Country Code Harmonization**: Comprehensive mapping system handling standard countries and regional entities
- ✅ **Population-Weighted Analysis**: Accounts for country population sizes using birth projections as weights
- ✅ **Custom Regional Entity Handling**: Processes non-standard geographic classifications
- ✅ **Reproducible Workflow**: Complete R Markdown pipeline from data import to final visualization
- ✅ **Professional Visualization**: Publication-ready charts with customizable styling
- ✅ **Flexible Output Formats**: Supports multiple output formats (HTML, PDF, GitHub markdown)
- ✅ **Comprehensive Documentation**: Detailed methodology and technical specifications

---

## Technical Specifications

### Data Processing Standards

**Coverage Period**: 
- Uses most recent data available per country within 2018-2022 timeframe
- Prioritizes data quality and recency over uniform time periods

**Population Weights**: 
- Based on UN World Population Prospects 2022 birth projections
- Accounts for demographic differences between countries
- Provides population-representative coverage estimates

**Country Classification**: 
- Groups based on U5MR progress status toward SDG targets
- "On-track": Countries with "Achieved" or "On Track" status
- "Off-track": All other countries with available data

**Missing Data Handling**: 
- Countries with incomplete coverage or demographic data excluded from calculations
- Maintains analytical rigor while maximizing sample size

### Quality Assurance Measures

- **Data Validation**: Automated checks for data completeness and consistency
- **Reproducibility**: Version-controlled analysis with documented dependencies
- **Transparency**: Open methodology with detailed code documentation
- **Robustness Testing**: Analysis verified with alternative approaches

---

## Limitations

### Analytical Limitations

- **Subnational Variation**: Analysis aggregates to country level, potentially masking within-country disparities
- **Data Consistency**: Assumes uniform reporting standards and methodologies across countries
- **Temporal Alignment**: Uses most recent available data, which may span different years across countries
- **Causal Inference**: Shows associations between track status and coverage, not causal relationships

### Data Limitations

- **Coverage Gaps**: Some countries excluded due to incomplete data
- **Reporting Standards**: Potential variations in indicator definitions and measurement approaches
- **Time Lag**: Analysis reflects historical data that may not capture most recent policy changes
- **Regional Representation**: Coverage may vary by geographic region and income level

### Methodological Considerations

- **Population Weights**: Birth projections used as proxy for need, may not reflect all relevant factors
- **Binary Classification**: Track status simplified to binary categories, losing nuanced classification detail
- **Static Analysis**: Cross-sectional analysis that doesn't capture trends over time

---

## Visualization Output

The analysis generates a comprehensive comparative visualization showing population-weighted coverage rates by track status.

### Chart Specifications

**Chart Type**: Grouped bar chart with percentage labels
**Color Scheme**: 
- On-track countries: Green (#1b9e77)
- Off-track countries: Orange (#d95f02)

**Key Features**:
- Clean, professional styling using `theme_minimal()`
- Percentage labels displayed on each bar
- Y-axis scaled from 0-100% with 25% intervals
- Clear legend and axis labels
- Publication-ready quality at 300 DPI

### Interpretation

The visualization clearly demonstrates:
1. **Consistent Gaps**: On-track countries outperform off-track countries on both indicators
2. **Larger SBA Gap**: Skilled birth attendance shows greater disparity than antenatal care
3. **Policy Implications**: Results suggest systematic differences in health system capacity

---

## Contributing

This repository represents a technical evaluation submission. For academic or policy use:

### Review Process

1. **Methodology Review**: Examine the three-step analytical approach
2. **Data Validation**: Verify data sources and processing steps
3. **Results Interpretation**: Consider findings in broader policy context
4. **Code Quality**: Review R code for reproducibility and best practices

### Extensions

Potential areas for further analysis:
- Regional breakdowns of coverage patterns
- Trend analysis over the 2018-2022 period
- Additional maternal health indicators
- Subnational analysis where data permits

---

## License

This analysis is prepared for UNICEF Data and Analytics technical evaluation purposes. 

**Usage Rights**: Academic and policy research applications welcomed with appropriate attribution.
**Data Sources**: Original data subject to respective organizational licenses and terms of use.

---

## Contact Information

**UNICEF Data and Analytics Education Team Technical Evaluation**

This repository maintains the complete analytical framework for the UNICEF consultancy assessment, focusing on maternal health coverage analysis using population-weighted statistical methods.

**Repository Purpose**: Demonstrate technical competency in:
- Multi-source data integration
- Advanced statistical analysis
- Professional data visualization
- Reproducible research practices

---

**Analysis Completed**: July 28, 2025  
**Data Sources**: UNICEF Global Data Flow, UN World Population Prospects 2022  
**Analysis Platform**: R Statistical Software with R Markdown  
**Repository**: https://github.com/Fikrewold/UNICEF_Consultancy_Assessment

---

*This analysis demonstrates systematic approaches to international health data analysis, emphasizing methodological rigor and policy-relevant insights.*
