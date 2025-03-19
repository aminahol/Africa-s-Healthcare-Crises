# Tackling Healthcare Crises in Africa

## Introduction  
This analysis was inspired by a **10alytics Datathon**, where a dataset on healthcare in Africa caught my attention. Although I didn’t participate, I found the dataset intriguing and decided to conduct an **independent exploratory analysis** to uncover **patterns, correlations, and insights** that could inform healthcare interventions.  

By analyzing **mortality rates, healthcare access, and socioeconomic factors**, this study highlights critical disparities in healthcare across African nations. The goal is to provide **data-driven insights** that can help **health decision-makers** develop effective strategies to improve healthcare access and reduce preventable deaths.  

---

## Dataset Overview  
The analysis is based on **six related datasets**, each offering valuable insights into healthcare and mortality trends across Africa.  

### 1. Mortality Data  
- Deaths by cause  
- Deaths by age group  

### 2. Healthcare Access  
- Number of medical doctors per **10,000 people**  

### 3. Geographical & Population Data  
- Country and continent classifications (**ISO 3166 codes**)  
- World population data  

### 4. Economic Indicator  
- Health expenditure as a **percentage of GDP**  

These datasets were sourced from **[Insert Data Source]** and were combined for a comprehensive analysis of Africa’s healthcare challenges.  

---

## Data Cleaning and Processing  
To ensure accuracy and consistency, **Power Query** was used for data cleaning. The key steps included:  

### 1. Handling Missing and Redundant Data  
- Checked for **errors, null values, and duplicates**.  
- Removed redundant data to maintain dataset integrity.  

### 2. Filtering for African Countries  
- Limited the dataset to **African nations only**.  
- Excluded **Mayotte, Western Sahara, and Saint Helena**, as these are overseas territories rather than fully recognized African nations.  

### 3. Standardizing Country Names  
- Adjusted country names to ensure consistency.  
  - Example: *Angola, Republic of* → *Angola*  

### 4. Restructuring Data for Consistency  
- **Health expenditure dataset**: Transposed to have all years in a single column.  
- **Deaths by age group dataset**: Reformatted to consolidate age groups into a single column.  

### 5. Removing Inconsistent Values  
- In the **doctors per 10,000 population dataset**, values like `>1` and `>100` were removed, as they were **incomplete or improperly formatted**.  

### 6. Creating the Data Model  
- Created a **date table** to link with datasets for time-based analysis.  
- Designed a **star schema**, linking the country table to other datasets for efficient querying.  

### Data Model Structure:  
![Data Model](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/26c5dc2c257b6d3220f6292e7e620f7425a9651f/visuals/Health%20Crises%20Data%20Model.png)

---

## Analysis & Key Insights  

### 1. Mortality Trends Over Time (1990–2019)  
![Data Model](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/main/visuals/HC%20Annual%20Trends.png?raw=true)
The **annual mortality trend** shows a **sharp increase in deaths in 1994**, followed by a **steady rise between 2000 and 2005**, peaking at **9.3 million deaths**.  

### 2. Population vs. Mortality Correlation  
![image](https://github.com/user-attachments/assets/41152057-c8f9-4690-a6e8-f5e2d3778b4f)


The scatter plot below highlights the relationship between **population size and mortality rates**. While there is a **general positive correlation**, some countries **show non-linear trends**, indicating other influencing factors.  
![image](https://github.com/user-attachments/assets/926c404e-5c56-4e96-97e0-31551c6229ed)


To further investigate, I used **Python (Plotly)** to highlight **countries with negative correlations** in red.  

#### Python Code for Population-Mortality Correlation Analysis:  

```python
import pandas as pd
import plotly.express as px
import numpy as np

# Load dataset (Power BI automatically provides 'dataset' as input)
df = dataset

# Convert data types
df["Population"] = pd.to_numeric(df["Population"], errors="coerce")
df["Total Deaths"] = pd.to_numeric(df["Total Deaths"], errors="coerce")

# Calculate correlation per country
correlation_results = df.groupby("Country").apply(
    lambda g: np.corrcoef(g["Population"], g["Total Deaths"])[0, 1]
).reset_index()
correlation_results.columns = ["Country", "Correlation"]

# Filter countries with negative correlation
negative_correlation_countries = correlation_results[correlation_results["Correlation"] < 0]

# Merge with original dataset to tag negative correlation countries
df["Negative Correlation"] = df["Country"].isin(negative_correlation_countries["Country"])

# Define color palettes
positive_colors = ["#1E3A8A", "#3B82F6", "#6366F1", "#908E9B"]
negative_colors = ["#E94754", "#8C4552"]

# Assign colors dynamically based on correlation
unique_countries = df["Country"].unique()
color_mapping = {}

for i, country in enumerate(unique_countries):
    if country in negative_correlation_countries["Country"].values:
        color_mapping[country] = negative_colors[i % len(negative_colors)]
    else:
        color_mapping[country] = positive_colors[i % len(positive_colors)]

# Create scatter plot with trendlines
fig = px.scatter(
    df,
    x="Population",
    y="Total Deaths",
    color="Country",
    trendline="ols",
    title="Total Deaths vs Population (All Countries & Years)",
    labels={"Population": "Population", "Total Deaths": "Total Deaths"},
    hover_data=["Country"],
    color_discrete_map=color_mapping
)

# Show plot
fig.show()

# Print countries with negative correlation
print("Countries with Negative Correlation:")
print(negative_correlation_countries)
```

![image](https://github.com/user-attachments/assets/99a5c368-a5fd-494b-8e53-cef32c689783)

Most countries exhibit a positive correlation between population size and total deaths, where mortality increases alongside population growth. In contrast, **Ethiopia, Tanzania, and Rwanda** (marked with red trendlines) show a **negative correlation**, meaning that despite population growth, total deaths have declined or remained stable. This suggests improvements in **healthcare access, economic conditions, or disease control**.  

**Rwanda presents a notable anomaly**, with a **sharp mortality spike in 1994**, coinciding with the **Rwandan Genocide**, highlighting the impact of conflict on mortality trends.
