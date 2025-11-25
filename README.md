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
| ---------------------- | --------------------------------------------- | ----------- |
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

A suite of DAX measures was created to support the analysis. These included overall metrics such as Total Deaths, Total Male Deaths, Total Female Deaths, as well as relative metrics like % Male Deaths and % Female Deaths. Analytical measures such as Rank by Total Deaths, Top N Countries, and Death Rate Insights were also developed. Additional measures focused on comparing male-to-female mortality ratios, providing a comprehensive toolkit for exploring population-level mortality trends.


