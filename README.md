# Tackling Healthcare Crises in Africa

## Introduction  
This analysis was inspired by a **10alytics Datathon**, where a dataset on healthcare in Africa caught my attention. Although I didn’t participate, I found the dataset intriguing and decided to conduct an independent exploratory analysis to uncover patterns, correlations, and insights that could inform healthcare interventions.  

By analyzing **mortality rates, healthcare access, and socioeconomic factors**, this study highlights critical disparities in healthcare across African nations. The goal is to provide data-driven insights that can help health decision-makers develop effective strategies to improve healthcare access and reduce preventable deaths.  

---

## Dataset Overview  
The analysis is based on **six related datasets**, each offering valuable insights into healthcare and mortality trends across Africa.  

### 1. Mortality Data  
- Deaths by cause  
- Deaths by age group  

### 2. Healthcare Access  
- Number of medical doctors per 10,000 people 

### 3. Geographical & Population Data  
- Country and continent classifications (ISO 3166 codes)  
- World population data  

### 4. Economic Indicator  
- Health expenditure as a percentage of GDP

These datasets were sourced from [Insert Data Source](https://github.com/aminahol/Africa-s-Healthcare-Crises/tree/fabea180f36e572672474a16f99fbd6bb02155cb/Dataset) and were combined for a comprehensive analysis of Africa’s healthcare challenges.  

---

## Data Cleaning and Processing  
To ensure accuracy and consistency, Power Query was used for data cleaning. The key steps included:  

### 1. Handling Missing and Redundant Data  
- Checked for errors, null values, and duplicates.  
- Removed redundant data to maintain dataset integrity.  

### 2. Filtering for African Countries  
- Limited the dataset to African nations only.  
- Excluded Mayotte, Western Sahara, and Saint Helena, as these are overseas territories rather than fully recognized African nations.  

### 3. Standardizing Country Names  
- Adjusted country names to ensure consistency.  
  - Example: *Angola, Republic of* → *Angola*  

### 4. Restructuring Data for Consistency  
- **Health expenditure dataset**: Transposed to have all years in a single column.  
- **Deaths by age group dataset**: Reformatted to consolidate age groups into a single column.  

### 5. Removing Inconsistent Values  
- In the **doctors per 10,000 population dataset**, values like `>1` and `>100` were removed, as they were incomplete or improperly formatted.  

### 6. Creating the Data Model  
- Created a **date table** to link with datasets for time-based analysis.  
-​In a star schema, the country dimension table is linked to other dimension tables

### Data Model Structure:  
![Data Model](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/26c5dc2c257b6d3220f6292e7e620f7425a9651f/visuals/Health%20Crises%20Data%20Model.png)

---

## Analysis & Key Insights  

### 1. Mortality Trends Over Time (1990–2019)  
![Data Model](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/main/visuals/HC%20Annual%20Trends.png?raw=true)
The annual mortality trend shows a **sharp increase in deaths in 1994**, followed by a **steady rise between 2000 and 2005**, peaking at **9.3 million deaths**.  

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

![Population vs. Mortality](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/b7a724cb6947497e658eddd01cc0658bd2a3b211/visuals/Population%20Vs%20Mortality%20(Plotly).png)

Most countries exhibit a positive correlation between population size and total deaths, where mortality increases alongside population growth. In contrast, **Ethiopia, Tanzania, and Rwanda** (marked with red trendlines) show a **negative correlation**, indicating that despite population growth, total deaths have declined or remained stable. This suggests improvements in **healthcare access, economic conditions, or disease control**.

Rwanda presents a notable anomaly, with a sharp mortality spike in 1994, coinciding with the **Rwandan Genocide**, highlighting the impact of conflict on mortality trends.

## Top Countries by %GDP Spent on Healthcare

![Top Countries by %GDP](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/228bc724c1177e765be01b5d777077f1acd8750e/visuals/Top%20Countries%20by%20%25GDP.png)

This chart displays the top five African countries that allocate the highest percentage of their GDP to healthcare.

## Economic Strength vs. Mortality

![Economic Strength vs. Mortality](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/9c411ecb4df59b3d27cc65833acf622825246e1f/visuals/Economic%20Strength%20vs%20Mortality.png)

While most countries in the visual maintain a relatively stable mortality rate regardless of GDP allocation, South Africa is an outlier, showing significant variations in mortality. This suggests that factors beyond healthcare spending, such as healthcare quality, infrastructure, or socio-economic conditions, may be influencing mortality trends.

## Leading Causes of Mortality and Trends Over the Years

![Leading Causes of Mortality](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/9c411ecb4df59b3d27cc65833acf622825246e1f/visuals/Leading%20Causes%20of%20Mortality.png)

Cardiovascular diseases are the leading cause, accounting for 37 million deaths. The chart below shows that this trend has been steadily increasing over the years.

![Leading Causes of Mortality](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/fa662edf288e9e86c648b54ab63196456fbf2554/visuals/Trend%20of%20Top%20Causes%20of%20Mortality.png)

Cardiovascular-related mortality has shown a steady increase over time, unlike other causes such as HIV and neonatal disorders, which have peaked, or diseases like diarrhea and lower respiratory infections, which have steadily declined.

## Mortality Distribution by Age Group

![Mortality by Age Group](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/9c411ecb4df59b3d27cc65833acf622825246e1f/visuals/Mortality%20Distribution%20by%20Age%20group.png)

Children under five years old have the highest mortality rate, totaling 116 million deaths and accounting for approximately 41% of total deaths, highlighting the urgent need for targeted interventions.

## Countries with the Highest Number of Doctors

![Countries with Most Doctors](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/9c411ecb4df59b3d27cc65833acf622825246e1f/visuals/Top%20Countries%20by%20Healthcare%20Workforce.png)

Nigeria and Egypt lead in the number of doctors. While this may be attributed to population size, countries like Tunisia and Algeria, which do not have the largest populations, also have a high number of doctors. This is why healthcare access (relative to population size) and its effect on mortality are explored in the visuals below.

## Healthcare Access and Its Relationship with Mortality

![Healthcare Access vs. Mortality](https://github.com/aminahol/Africa-s-Healthcare-Crises/blob/9c411ecb4df59b3d27cc65833acf622825246e1f/visuals/HealthCare%20Access%20vs%20Mortality.png)

Both Tunisia and Algeria are among the top five countries in healthcare access. However, the data shows a weak correlation between healthcare access and mortality rates and this indicates that while health care access is essential, it alone does not significantly determine mortality rates.



