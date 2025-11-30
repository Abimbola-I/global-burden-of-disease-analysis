# Global Disease Burden Analysis




## Project Overview

The health and productivity of any community — and ultimately entire nations — depend heavily on the stability of their population’s wellbeing. When health declines, so does social and economic progress. Understanding patterns of mortality is therefore crucial for guiding policy, resource allocation, and long-term development.

This project explores the Global Burden of Disease by analyzing mortality patterns across 187 countries using data from five separate benchmark years: 1970, 1980, 1990, 2000, and 2010
(These are independent yearly snapshots — not continuous decades).

The goal of this analysis is to uncover high-level global health insights, including:

- Total deaths globally

- Country-level mortality burden (highest vs lowest affected countries)

- Differences in deaths between males and females

- Mortality variations across age groups

- How global deaths shifted across these five selected years

Using Power BI, the dataset was cleaned, modeled, and analyzed to reveal large-scale patterns in global mortality. Although the dataset does not include detailed disease categories or underlying causes of death, it provides valuable macro-level insights into where, when, and which demographic groups were most affected across these five points in time.


## Motivation

I completed this project to strengthen my skills in Power BI and data storytelling, enhance my ability to analyze population-level health trends, and practice delivering a complete end-to-end data analytics workflow.

This project also contributes to my portfolio as a healthcare data analyst, providing an opportunity to explore global mortality patterns and translate complex data into meaningful, actionable insights.


## Dataset Description

Dataset name: Global Burden of Disease Dataset
Time span: 1970 · 1980 · 1990 · 2000 · 2010
Countries: 187
Columns: 7
Rows: 58,906
Source: LinkedIn public dataset

Columns Included
| Column                 | Description                                   | Type        |
| :--------------------- | :-------------------------------------------- | :---------- |
| Country Code           | Name of country                               | Categorical |
| Country Name           | Three–letter code                             | Categorical |
| Year                   | Observation year                              | Numeric     |
| Age Group              | Age bracket                                   | Categorical |
| Sex                    | Male/Female                                   | Categorical |
| Number of Deaths       | Recorded deaths in that year & group          | Numeric     |
| Death Rate per 100,000 | Mortality rate normalized per 100k population | Numeric     |


## Data Preparation (ETL)

The data preparation phase involved a thorough ETL process, starting with cleaning, standardization, and validation. Missing values were checked and addressed, age group labels were standardized for consistency, country names were harmonized across the dataset, numeric columns were validated, and duplicate records were removed. To facilitate analysis, several dimension tables were created, including Country_Dim, Sex_Dim, AgeGroup_Dim, and Year_Dim.

For the data modeling, a star schema was implemented, with the dimension tables connecting to a central fact table, Global Mortality Fact. This structure provided an efficient and organized framework for analytical queries.

In this project, more than 20 custom DAX measures were created to drive all analytics, rankings, demographic insights, and time-trend evaluations. These measures cover core aggregations (such as Total Deaths, Total Male/Female Deaths), percentage distributions (e.g., % of Total Deaths, % Male/Female Deaths), advanced ranking using RANKX (e.g., Rank Total Country by Deaths, Rank Age Group by Death Rate), and parameter-driven logic (Top/Bottom toggles and dynamic Top-N analysis). Together, these DAX measures form the analytical backbone of this mortality report, enabling dynamic filtering, interactive exploration, and insightful comparisons across countries, genders, years, and age groups.



### Examples of Core Measures Used

```DAX

Total Deaths = SUM(Global_Fact[Number of Deaths])


Total Male Deaths = 
CALCULATE(
    [Total Deaths],
    FILTER(Sex_Dim, Sex_Dim[Sex] = "Male")
)


Total Female Deaths = 
CALCULATE(
    [Total Deaths], 
    FILTER(Sex_Dim[Sex] = "Female")
)


Total Deaths Rate Per 100,000 = SUM(Global_Fact[Death Rate Per 100,000])

```

### Examples of Percentage Measures

```DAX

% Of Total Deaths =
VAR _TotalDeathsAllAgeGroup = 
    CALCULATE(
        [Total Deaths],
        ALL('Age _Dim'[Age Group]))

RETURN
DIVIDE(
    [Total Deaths],
     _TotalDeathsAllAgeGroup
    
    )


% Of Male Deaths =
DIVIDE(
    CALCULATE([Total Deaths], Sex_Dim[Sex] = "Male"),
    CALCULATE([Total Deaths])
)


% Of Female Deaths =
DIVIDE(
    CALCULATE([Total Deaths], Sex_Dim[Sex] = "Female"),
    CALCULATE([Total Deaths])
)

```

### Examples of Ranking Measures

```DAX

Rank Total Country by Total Deaths = 
               VAR _topdeathspercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths], ,DESC)))

                VAR _bottomdeathspercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths], ,ASC)))


                VAR _ranking = 
                    IF(SELECTEDVALUE('Top & bottom'[Value]) = "Top",_topdeathspercountry,
                    _bottomdeathspercountry)


                RETURN IF(_ranking <= 'Top N Parameter'[Top N Parameter Value],[Total Deaths]) 


Rank Total Country by Death Rate Per 100,000 = 
                VAR _topdeathratepercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths Rate Per 100,000], ,DESC)))

                VAR _bottomdeathratepercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths Rate Per 100,000], ,ASC)))


                VAR _ranking = 
                    IF(SELECTEDVALUE('Top & bottom'[Value]) = "Top",_topdeathratepercountry,
                    _bottomdeathratepercountry)


                RETURN IF(_ranking <= 'Top N Parameter'[Top N Parameter Value],[Total Deaths Rate Per 100,000]) 


Rank Gender by Total Deaths = 
               VAR _topdeathsbygender = 
                    IF(ISINSCOPE(Sex_Dim[Sex ID]),
                    (RANKX(ALL(Sex_Dim[Sex ID]),[Total Deaths], ,DESC)))

                VAR _bottomdeathsbygender = 
                    IF(ISINSCOPE(Sex_Dim[Sex ID]),
                    (RANKX(ALL(Sex_Dim[Sex ID]),[Total Deaths], ,ASC)))


                VAR _ranking = 
                    IF(SELECTEDVALUE('Top & bottom'[Value]) = "Top",_topdeathsbygender,
                    _bottomdeathsbygender)


                RETURN IF(_ranking <= 'Top N Parameter'[Top N Parameter Value],[Total Deaths]) 

Rank Total Country by Death Rate Per 100,000 = 
                VAR _topdeathratepercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths Rate Per 100,000], ,DESC)))

                VAR _bottomdeathratepercountry = 
                    IF(ISINSCOPE(Country_Dim[Country Name]),
                    (RANKX(ALL(Country_Dim[Country Name]),[Total Deaths Rate Per 100,000], ,ASC)))


                VAR _ranking = 
                    IF(SELECTEDVALUE('Top & bottom'[Value]) = "Top",_topdeathratepercountry,
                    _bottomdeathratepercountry)


                RETURN IF(_ranking <= 'Top N Parameter'[Top N Parameter Value],[Total Deaths Rate Per 100,000]) 

```

### Examples of Extreme Value Measures

```DAX

Highest Number of Deaths = 
MAXX(
   VALUES(Global_Fact[Country ID]),
   [Total Deaths]
)

Country_With_Max_Deaths = 
VAR _max = [Highest Number of Deaths]
RETURN
CALCULATE(
    SELECTEDVALUE(Country_Dim[Country Name]),
    FILTER(
        VALUES(Country_Dim[Country Name]),
        [Total Deaths] = _max
    )
)

```


## Analysis & Key Insights

### 1. Total Global Mortality

_Across the five reporting years (1970, 1980, 1990, 2000, 2010), the dataset recorded 237 million total deaths globally._


![Screenshot (228) (1)](https://github.com/user-attachments/assets/ce48d1cc-9797-45b4-a4ae-766beb1f9079)


### 2. Death Rate per 100,000 (Mortality Risk)

Death rate per 100,000 declined in later years even though total deaths increased, that is people became less likely to die even though more people existed globally.

This pattern generally indicates:
- _Improved access to healthcare_
- _Better disease prevention and treatment_
- _Faster population growth than mortality growth_


### 3. Highest & Lowest Burden Countries

- _India accounted for the highest share of global deaths (≈37.5%)._
- _Marshall Islands recorded the lowest share of global deaths (≈9.65%)._

This contrast highlights the wide differences in population size and health outcomes across countries.


![Screenshot (227)](https://github.com/user-attachments/assets/5e9803ea-8c23-4c48-9183-4359e93ed2d0)


### 4. Country-Level Trends

_Geospatial mapping revealed clear differences in mortality across regions, with high-population countries contributing much larger portions of global deaths._


### 5. Gender Insights

- _Male deaths: 53.94%._
- _Female deaths: 46.06%._

Males consistently showed a higher mortality burden across all years, mirroring global patterns where men often face higher risks due to biological, behavioral, and occupational factors.


![Screenshot (226)](https://github.com/user-attachments/assets/cac72df7-2619-475f-8144-8680426dc9ca)


### 6. Age Group Mortality Patterns

Highest-mortality groups:
- _80+ years — 15.91%._
- _75–79 years — 8.67%._
- _70–74 years — 8.66%._

These reflect the natural rise in mortality among older populations.

Lowest-mortality groups:
- _10–14 years — 0.95%._
- _5–9 years — 1.45%._
- _15–19 years — 1.51%._

This mirrors global patterns where childhood and teenage mortality tends to be low due to stronger immunity and fewer chronic disease risks.


### 7. Trends Over Time

Total deaths increased over the years (population growth effect) while the death rate per 100k decreased, reflecting improvements in global health and survival.
These opposing trends together provide a more realistic view of global mortality patterns.


![Screenshot (225)](https://github.com/user-attachments/assets/917c77b5-b684-4328-b77e-fba653748d8d)


## Report Pages 

The analysis was built in Power BI, starting with a custom-designed landing page that guides users into various analytical sections. The report also includes dynamic parameters to switch between metrics and control ranking outputs across countries, years, age groups, and sexes.

### 1. Overview

- Total deaths.
- Death Rate Per 100,000.
- Total countries.
- Percentage of Total Deaths and Total Deaths by Country
- Total Deaths by Gender
- Percentage of Total Deaths and Total Deaths by Age group 
- Total Deaths and Death Rate by Year

### 2️. Geographical Insights

- Top & bottom N countries
- Country filter parameter
- Top 5 Countries by Total Deaths
- Percentage of Male Deaths within top 5 countries and global percentage of male deaths from that country

### 3️. Demographic Breakdown

- Percentage of Male vs Female Deaths 
- Percentage of Male and Female Deaths by Age group
- Percentage of Total Deaths Age group
- Total Deaths by Sex

### 4️. Mortality Trends Over Time

- Top 5 countries by death rate per year (1970,1980,1990,2000,2010)
- Trend of Death rate per 100k and Total Deaths


## Tools Used

- Power BI Desktop
- Power Query (M)
- DAX
- Excel (initial review)
- GitHub for documentation

 
 ## Challenges & Limitations

- **Limited dataset scope**: dataset contains only five isolated years (1970, 1980, 1990, 2000, 2010), restricting long-term trend modelling.
- **No cause-of-death detail**: the dataset reports only total deaths, preventing disease-specific insights.
- **No information on healthcare access, socioeconomic status, or policy changes**: this limits deeper interpretation of why mortality patterns changed.
- **Age-group and sex breakdown only**: absence of additional demographic features restricts richer segmentation.


## Recommendations

1. **Strengthen Health Policies in High-Burden Countries.**

Countries with consistently high mortality levels, particularly India, Nigeria, China, and the United States, should intensify national health policies that address the systemic drivers of mortality. This includes expanding access to essential healthcare services, improving emergency response capacity, and increasing investments in preventive care.

2. **Prioritize Early Interventions for High-Risk Age Groups.**

Mortality is most concentrated among adults aged 70–80+ years, highlighting the need for broader access to geriatric care, routine screenings for chronic diseases, improved management of age-related conditions, and community-level support systems for ageing populations. Addressing the health needs of these high-risk groups can meaningfully reduce the overall national mortality burden.

3. **Improve Youth & Adolescent Health Programs.**

Although deaths among younger age groups (5–19 years) are relatively low, youth populations remain highly vulnerable to nutritional deficits, infectious diseases, road accidents, environmental hazards, and limited access to preventive healthcare. Strengthening health programs for children and adolescents can help reduce avoidable deaths and promote healthier adult populations in the future.

4. **Increase Investments in Healthcare Infrastructure.**

While the available data did not provide specific information on existing healthcare infrastructure, countries with high mortality could benefit from investments in primary healthcare facilities, developing a skilled healthcare workforce, and establishing disease surveillance and early detection systems. Where such infrastructure is lacking or underdeveloped, prioritizing these areas can support better health outcomes and contribute to a reduction in mortality over time.


## How to View This Project

You can view this project by downloading any of the available files below:

- PDF Version: Download the PDF report using the link provided.

- PBIX Version: (Optional) Download the PBIX file if you want to explore the visuals directly in Power BI.


## Conclusion

This analysis highlights broad global mortality patterns across countries, sexes, and age groups over a 5 year period.
Despite limitations, the project demonstrates strong Power BI skills in:

- Data Cleaning
- Data modeling
- DAX
- Data storytelling
- Report design
- Insight communication


## About the Analyst

I leverage data to create impactful solutions that support sustainable growth. I am particularly passionate about driving progressive results in healthcare, especially within public health, and I also have a strong interest in digital health.

### You can connect with me on LinkedIn : linkedin.com/in/rukayat-olanrewajuabimbola
