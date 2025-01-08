# U.S. Cencus Bureau ACS Subject Tables Data Pipeline

Pipeline for cleaning and merging American Community Survey (ACS) Subject Tables data from the US Census Bureau.

## Overview

This pipeline processes multiple years of ACS Subject Tables data through four main stages:
1. Raw data and metadata collection
2. Label matching between years
3. Label merging
4. Data merging and filtering

## Prerequisites

- Python 3.x
- Jupyter Notebook
- Required packages: pandas, numpy, requests

## Pipeline Stages

### 1. Data Collection (`get_raw_data_1.ipynb`)

Collects raw data and metadata via Census API:
- **Raw Data**: Subject tables for specified years
  - Output: `ACS-{table}-{year}-Data.csv`
  - Location: `Raw_Data/`
- **Type Information**: Variable types (int/float)
  - Output: `{year}-type.json`
  - Location: `Metadata/`
- **Metadata**: Variable information from Census API
  - Source: `https://api.census.gov/data/[YEAR]/acs/acs5/subject/variables`
  - Output: `ACS-ST5Y{year}-Metadata.csv`
  - Location: `Metadata/`

### 2. Label Matching (`match_labels_2.ipynb`)

Matches variable labels between consecutive years using hierarchical matching:

#### Matching Process
1. Text Cleaning: Standardizes naming conventions
2. Exact Match (flag = 1.0): Identical labels and types
3. Content Match (flag = 0.9): Same parts in different order
4. Partial Matches (flags 0.8-0.2): Various combinations of first/last parts

Output:
- File: `match_{year}_{year+1}.xlsx`
- Location: `YtoY_diff/`

#### Special Cases
- Custom handling for 2016-2017 and 2018-2019 transitions
- Uses `find_difference` function to identify and resolve discrepancies

### 3. Label Merging (`merge_labels_3.ipynb`)

Combines matched labels across years:
- Inner joins all year-to-year matches
- Creates margin of error (M) variables from estimates (E)
- Output: `combined_id_E_M.csv`

### 4. Data Merging (`merge_subject_tables_4.ipynb`)

Final data processing and filtering:
1. Filters tables (>30,000 values)
2. Applies California zipcode filter (90001-96162)
3. Optional: Filters to 870 CA zipcodes from Costar dataset
4. Merges yearly data vertically

Output:
- File: `Merged_{table}.csv`
- Location: `Merged_Data/`

## Reference Links

- [Census API Notes](https://www.census.gov/data/developers/data-sets/acs-1year/notes-on-acs-estimate-and-annotation-values.html)
- [ACS Documentation](https://www.census.gov/data/developers/data-sets/acs-5year.html)
