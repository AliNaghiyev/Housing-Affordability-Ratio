# Housing-Affordability-Ratio
#  Global Housing Affordability
### *Are homes actually getting more expensive, or are people just earning more?*
## Project Overview
The "Global Housing Affordability Crisis" is a popular headline, but measuring it requires more than looking just house prices. This project analyzes the decoupling of **Real House Price Indices (HPI)** from **Real Household Disposable Income** across 30+ major economies.

The goal was to build an automated data pipeline and an interactive dashboard to answer: **Which countries are facing the most severe housing affordability crises today?**
The important visualizations: [Visaulizations](https://github.com/AliNaghiyev/Housing-Affordability-Ratio/blob/main/Visualization/Housing%20Affordability.pdf)
## Key Findings
Based on the analysis of OECD data (1975–2024):

1.  **The Great Decoupling:** In many developed economies, house prices have significantly outpaced wage growth since 2015. The "Affordability Gap" is clearly visible in the global trend line.
2.  **Most Unaffordable Countries:**
    * **United Kingdom:** Currently shows the highest disparity between income and house prices.
    * **Netherlands & Hungary:** Indicating a severe crisis of the housing market.
3.  **Market Corrections:** Recent data (Latest HPI Growth) shows negative trends in several high-risk countries.

## Technical Workflow

### 1. Data Engineering (Python & Pandas)
Raw data from the OECD was processed using Python to ensure a fair comparison between different economies.
* **Data Cleaning:** Filtered for "Quarterly" frequency and "Real House Price Index" to remove noise.
* **Resampling:** Income data (Annual) was upsampled to Quarterly frequency using the `ffill` (forward fill) method to match Housing data.
* **Normalization (Base 100):** Crucial step. Both Income and HPI were rebased to 100 starting at the first common date (e.g., 2002 or 2015). This allows us to compare *rates of change* rather than currency values.
* 
    * *Formula:* `(Current Value / Base Value) * 100`

### 2. Visualization (Power BI)
The data was exported to Power BI for interactive analysis.
* **Dynamic Time Intelligence:** Used DAX to handle time slicers correctly.
    * *Challenge:* Slicers usually calculate the average of the whole selected period.
    * *Solution:* Created `Latest` measures to ensure KPI cards always show the *current* crisis status, even if 20 years of history is selected in the timeline.
* **Custom Measures:**
    * **Affordability Ratio:** `(House Price Index / Income Index) * 100`
    * **YoY Growth:** Calculated year-over-year percentage change to identify trends.
* **UX Design:** Implemented dynamic coloring (Red/Green gradients) on maps and charts to visually highlight "danger zones" (Affordability Ratio > 100).
The used Jupyter codes:[Codes](https://github.com/AliNaghiyev/Housing-Affordability-Ratio/blob/main/codes/Housing%20Affordability.ipynb)
## Key DAX Formulas
To ensure the KPI cards reflect the *end* of the selected period (the current status) while the charts show the *history*:

```dax
// Latest Affordability Score (KPI Card)
Latest Affordability Score = 
CALCULATE(
    [Avg Affordability], 
    LASTDATE('Dim_Date'[Date])
)

// Year-over-Year Growth (Dynamic)
HPI YoY Growth % = 
VAR CurrentVal = [Avg HPI]
VAR PreviousYearVal = CALCULATE([Avg HPI], SAMEPERIODLASTYEAR('Dim_Date'[Date]))
RETURN
DIVIDE(CurrentVal - PreviousYearVal, PreviousYearVal, 0)
