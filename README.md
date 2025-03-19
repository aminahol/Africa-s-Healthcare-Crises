Tackling Healthcare Crises in Africa
Introduction
This analysis was inspired by a 10alytics Datathon, where a dataset on healthcare in Africa caught my attention. Though I didn’t participate, I conducted an independent exploratory analysis to uncover patterns, correlations, and insights that can guide data-driven healthcare interventions.

By examining mortality rates, medical access, and socioeconomic factors, this study highlights critical disparities in healthcare across African nations. The findings aim to support health sector leaders in Africa, offering evidence-based insights to guide policy decisions, improve healthcare access, and reduce preventable deaths.

Data Description
This analysis is based on a dataset comprising six related datasets, each providing valuable insights into healthcare and mortality trends across African countries. The datasets include:

1. Mortality Data:
Number of deaths by cause
Number of deaths by age group
2. Healthcare Access:
Number of medical doctors per 10,000 people
3. Geographical & Population Data:
Country and continent classifications (ISO 3166 codes)
World population figures
4. Economic Indicator:
Health expenditure as a percentage of GDP
These datasets were sourced from [Insert Data Source] and combined to facilitate a comprehensive exploration of healthcare crises in Africa.

Data Cleaning and Processing
Before starting the analysis, I carried out data preprocessing to ensure accuracy and consistency. All data cleaning was performed in Power Query, following a structured approach:

1. Handling Missing and Redundant Data
Checked for errors, null values, and duplicates.
Removed redundant data to maintain dataset integrity.
2. Filtering for African Countries
Limited the dataset to African nations.
Excluded Mayotte, Western Sahara, and Saint Helena as they are overseas territories rather than fully recognized African nations.
3. Standardizing Country Names
Extracted only the country name before delimiters.
Example: Angola, Republic of → Angola
4. Restructuring Data for Consistency
Health expenditure dataset: Transposed to have all years in a single column.
Deaths by age group dataset: Reformatted to consolidate age groups into a single column.
5. Removing Inconsistent Values
In the doctors per 10,000 population dataset, values like ">1" and ">100" were removed, as they were imprecise and not actual numerical data.
6. Creating and Linking Tables
Created a date table and linked it to the datasets for time-based analysis.
Established a star schema relationship, linking the country table to the datasets, treating them as fact tables for structured querying.
Data Model
Below is the star schema used for this analysis:


Analysis
Annual Mortality Trend (1990–2019)
This visual presents the annual mortality trend from 1990 to 2019. A sharp increase in deaths was observed in 1994, followed by a steady rise between 2000 and 2005, peaking at 9.3 million deaths.

Population vs. Mortality Correlation
The image below shows the top 5 most populous African countries, with Nigeria accounting for 35% of the total population. The scatter plot also examines the correlation between population size and mortality rates, revealing a general positive correlation.

However, some countries exhibit non-linear patterns, suggesting other influencing factors beyond population size. To further investigate, I used Python (Plotly) to create a custom visualization highlighting countries with negative correlations.
