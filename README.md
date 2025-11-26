# Global Disease Burden Analysis

![front-view-covid19-concept-school](https://github.com/user-attachments/assets/0c04e354-b52e-4af4-9107-2ea5347d06fd)


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


