## Attacks on Power Grids Analysis
**Authors**: Gabriel Cha, Jun-Hee Hwang
**Tools**: Python, Pandas, Plotly, Numpy

## Introduction
Power outages can have a significant economic impact, as they can disrupt business operations and cause loss in revenue. 

Specifically, there has been a rise of intentional attacks on power grids. Interestingly, these attacks have been clustered around certain US States, such as Washington and Iowa. **Are these states experiencing a rise in attacks by chance alone, or is there something bigger at play?**

## Cleaning and EDA
### Data Cleaning

**Conversion from Excel to csv file**

We first converted the given file to csv file. In the process, we deleted title and description so that it is readable in cvs format. 

**Fill NaN values in OUTAGE.START.DATE**

We found out that some data in `OUTAGE.START.DATE` column is missing while the column `YEAR` of the same row is available. We first fill out some of the missing values in `OUTAGE.START.DATE` column using the data available in the `YEAR` column. 

**Convert string into Timestamp object**

we converted `OUTAGE.START.TIME`, `OUTAGE.START.DATE`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.DATE` columns into `OUTAGE.START` and `OUTAGE.RESTORATION`, which are series of Timestamp object of the start and restoration time. 

**Fill NaN values in OUTAGE.DURATION**

Since we are planning to use this column frequently throughout the analysis, we decided to fill out NaN values with the median of non-NaN values in each causality in `CAUSE.CATEGORY`. We chose to use median because we found that there are a lot of outliers in `OUTAGE.DURATION` column. 

**Fill NaN values in TOTAL.PRICE**

Since we are planning to use this column in graphic analysis, we decided to fill out NaN values in `TOTAL.PRICE` with the median of non-NaN values in each state in `POSTAL.CODE`. 

**Cleaned dataframe**

|   YEAR | POSTAL.CODE   | U.S._STATE   | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   TOTAL.PRICE | OUTAGE.START        | OUTAGE.RESTORATION   |   category_median |   price_median |   TOTAL.LOSS |
|-------:|:--------------|:-------------|:-------------------|:-------------------|------------------:|--------------:|:--------------------|:---------------------|------------------:|---------------:|-------------:|
|   2011 | MN            | Minnesota    | normal             | severe weather     |              3060 |          9.28 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              2460 |           9.19 |   4732.8     |
|   2014 | MN            | Minnesota    | normal             | intentional attack |                 1 |          9.28 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                56 |           9.19 |      1.54667 |
|   2010 | MN            | Minnesota    | cold               | severe weather     |              3000 |          8.15 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              2460 |           9.19 |   4075       |
|   2012 | MN            | Minnesota    | normal             | severe weather     |              2550 |          9.19 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              2460 |           9.19 |   3905.75    |
|   2015 | MN            | Minnesota    | warm               | severe weather     |              1740 |         10.43 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              2460 |           9.19 |   3024.7     |

### Univariate Analysis
**Outage Distribution**

As shown in the distribution, most of the outages are clustered in the front. This means that majority of outages lasted less than 2000 minutes. More specifcally, 64.67% of all outages are in the very first `[0, 1999)` bin. Outlier: The longest outage lasted 108653 minutes (roughly 4527 Days and 5 Hours) caused from a fuel supply emergency in Wisconsin.


**Causality Barchart**

As shown in the distribution, outages are most commonly caused from severe weather. Specifcally, 49.74% of outages are due to severe weather. Intentional attacks is the second main leading cause outages, making up 27.25% of the recorded outages in the dataset.

**Location of Outages** 

As shwon in the bar chart, California is the leading state with the most outages (13.69% of all recorded outages). The second leading state with the most outages is Texas, making up 8.28% of all outages. Alaska comes last with one reported outage. 


## Assessment of Missingness
### Missingness Dependency
**Dependency of OUTAGE.DURATION to CAUSE.CATEGORY**

Here, we measured the dependency of the missing values in the `OUTAGE.DURATION` column to `CAUSE.CATEGORY` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0.001 as p-value. Since p-value is below five percent, we conclude that the missing value in `OUTAGE.DURATION` column is highly dependent to `CAUSE.CATEGORY` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_Causality.html" width=800 height=600 frameBorder=0></iframe>

**Dependency of OUTAGE.DURATION to YEAR**

Here, we measured the dependency of the missing values in the `OUTAGE.DURATION` column to `YEAR` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0 as p-value. Since p-value is zero, we conclude that the missing value in `OUTAGE.DURATION` column is extremely dependent to `YEAR` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_Year.html" width=800 height=600 frameBorder=0></iframe>

**Independency of OUTAGE.DURATION to POSTAL.CODE**

Here, we measured the independency of the missing values in the `OUTAGE.DURATION` column to `POSTAL.CODE` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0.245 as p-value. Since p-value is above five percent, we conclude that the missing value in `OUTAGE.DURATION` column is independent to `POSTAL.CODE` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_Location.html" width=800 height=600 frameBorder=0></iframe>



## Hypothesis Testing


