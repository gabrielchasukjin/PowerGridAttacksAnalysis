## Attacks on Power Grids Analysis
**Authors**: Gabriel Cha, Jun-Hee Hwang\
**Tools**: Python, Pandas, Plotly, Numpy

## Introduction
Power outages can have a significant economic impact, as they can disrupt business operations and cause loss in revenue. 

Specifically, there has been a rise of intentional attacks on power grids. Interestingly, these attacks have been clustered around certain US States, such as Washington and Iowa. **Are these states experiencing a rise in attacks by chance alone, or is there something bigger at play?**

#### Rows and Columns

There are 1534 rows and 12 columns in the dataset that are relevant to the question. 

The column `YEAR` is used to see when the rate of intentional attacks on power grids began to rise.\
Columns `U.S._STATE` and `POSTAL.CODE` are used to determine where the attacks are clustered.\
Columns `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.TIME`, and `OUTAGE.RESTORATION.DATE` are used to see the time it took to restore the power.\
Column `CAUSE.CATEGORY` is used to see what kind of causality there are and the proportion of intentional attacks among them.\
Column `TOTAL.PRICE` and `CUSTOMERS.AFFECTED` are used to calculate total loss from the power outage.\
Column `COST.LOSS` is newly added column to see loss yielded from the power outage. It'll show the loss from power outage caused by intentional attacks.


## Cleaning and EDA

### Data Cleaning

To ensure that the insights and conclusions drawn from the data are accurate and reliable, we cleaned our dataset in the following manner:\
**1. Excel to CSV Format**\
We converted the Excel file to CSV format. Since CSV only accepts data points seperated by commas, we deleted the title and description in the Excel file so that the data is readable into pandas DataFrame.\
**2. Filter Out Unnecessary Columns**\
Out of the 55 columns, we kept 11 and created 1 new column. The reason for dropping over a dozen columns were because they were unnecessary to answering our Hypothesis Question. For example, we did not require the percentage of inland water area in the state. While it may be important data points, it was unnecessary for the scope of this project.\
**3. Fill NaN values in OUTAGE.START.DATE**\
We discovered some dates in `OUTAGE.START.DATE` column are missing. However, the `YEAR` column still provides some level of information of when the outage occured. Therefore, we filled the missing `OUTAGE.START.DATE` values with the given `YEAR`. \
**4. Combine Time and Date into Timestamp Object**\
The power outage start date and time is given by `OUTAGE.START.TIME` and `OUTAGE.START.DATE`. We combined the two columns into a new `pd.Timestamp` column called `OUTAGE.START`. Likewise, we combined `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.DATE` columns into a new `pd.Timestamp` column called `OUTAGE.RESTORATION`.\
**5. Fill NaN values in TOTAL.PRICE**\
Since we are planning to use this column in graphic analysis, we decided to fill out NaN values in `TOTAL.PRICE` with the median of non-NaN values in each state in `POSTAL.CODE`.\
**6. New Column Calculating the Total Loss ($Dollar/Killowatt)**\
The `TOTAL.PRICE` provides information on the average monthly electricity price in the U.S. state (cents/kilowatt-hour). By multiplying `TOTAL.PRICE` by `OUTAGE.DURATION`, we can calculate the amount of money (cent/kilowatt-hour) the electricity comapny could have earned during the time of outage.

#### Clean Dataframe

|   YEAR | POSTAL.CODE   | U.S._STATE   | CLIMATE.CATEGORY   | OUTAGE.START.DATE         | OUTAGE.START.TIME   | OUTAGE.RESTORATION.DATE    | OUTAGE.RESTORATION.TIME   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   TOTAL.PRICE |   CUSTOMERS.AFFECTED |
|-------:|:--------------|:-------------|:-------------------|:--------------------------|:--------------------|:---------------------------|:--------------------------|:-------------------|------------------:|--------------:|---------------------:|
|   2011 | MN            | Minnesota    | normal             | Friday, July 01, 2011     | 5:00:00 PM          | Sunday, July 03, 2011      | 8:00:00 PM                | severe weather     |              3060 |          9.28 |                70000 |
|   2014 | MN            | Minnesota    | normal             | Sunday, May 11, 2014      | 6:38:00 PM          | Sunday, May 11, 2014       | 6:39:00 PM                | intentional attack |                 1 |          9.28 |                  nan |
|   2010 | MN            | Minnesota    | cold               | Tuesday, October 26, 2010 | 8:00:00 PM          | Thursday, October 28, 2010 | 10:00:00 PM               | severe weather     |              3000 |          8.15 |                70000 |
|   2012 | MN            | Minnesota    | normal             | Tuesday, June 19, 2012    | 4:30:00 AM          | Wednesday, June 20, 2012   | 11:00:00 PM               | severe weather     |              2550 |          9.19 |                68200 |
|   2015 | MN            | Minnesota    | warm               | Saturday, July 18, 2015   | 2:00:00 AM          | Sunday, July 19, 2015      | 7:00:00 AM                | severe weather     |              1740 |         10.43 |               250000 |

### Univariate Analysis
**Outage Distribution**

<iframe src="assets/Outage_Duration_Distribution.html" width=700 height=500 frameBorder=0></iframe>

As shown in the distribution, most of the outages are clustered in the front. This means that majority of outages lasted less than 2000 minutes. More specifcally, 64.67% of all outages are in the very first `[0, 1999)` bin. Outlier: The longest outage lasted 108653 minutes (roughly 4527 Days and 5 Hours) caused from a fuel supply emergency in Wisconsin.

**Causality Barchart**

<iframe src="assets/Cause_of_Outage" width=700 height=500 frameBorder=0></iframe>

As shown in the distribution, outages are most commonly caused from severe weather. Specifcally, 49.74% of outages are due to severe weather. Intentional attacks is the second main leading cause outages, making up 27.25% of the recorded outages in the dataset.

**Location of Outages** 

<iframe src="assets/TVD_Duration_State_Choropleth.html" width=700 height=500 frameBorder=0></iframe>

As shown in the bar chart, California is the leading state with the most outages (13.69% of all recorded outages). The second leading state with the most outages is Texas, making up 8.28% of all outages. Alaska comes last with one reported outage. 

### Bivariate Analysis

**Time (Year) vs Number of Outages**

<iframe src="assets/Outage_Occurrence_Year.html" width=700 height=500 frameBorder=0></iframe>

We wanted to determine the empirical relationship between Time and Number of Outages. As shown in the line plot, the number of outages generally increased as time increased until 2011 when the number of outages peaked and graudually decreased from then on. 

We became interested in what had caused this peak in 2011. As a result, we filtered the dataset to observe the leading cause of outages in that year. 

| CAUSE.CATEGORY                |   MONTH |
|:------------------------------|--------:|
| equipment failure             |       4 |
| fuel supply emergency         |       6 |
| intentional attack            |     121 |
| islanding                     |       3 |
| public appeal                 |      16 |
| severe weather                |     107 |
| system operability disruption |      12 |

As shown in the DataFrame, intentional attacks was the leading cause of outages in the year 2011. Interestingly, in the previous year (2010) there were no reported outages caused by intentional attacks. 
To see if these attacks were common in the previous years, we graphed a line plot of the total number of attacks against time. 

<iframe src="assets/Outage_Occurrence_State.html" width=700 height=500 frameBorder=0></iframe>

### Interesting Aggregates
We became curious if a certain state was responsible for the rise in intentional attacks on the power grid. To answer this question, we grouped the DataFrame by state and pivoted the table based on the cause of outages so that we can output a conditional distribution of U.S. States given outage cause.

| U.S._STATE           |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:---------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Alabama              |              0      |                  0      |               0.0024 |      0      |          0      |           0.0066 |                          0      |
| Alaska               |              0.0167 |                  0      |               0      |      0      |          0      |           0      |                          0      |
| Arizona              |              0.0667 |                  0      |               0.0431 |      0      |          0      |           0.0052 |                          0.0157 |
| Arkansas             |              0.0167 |                  0      |               0.0144 |      0.0217 |          0.1014 |           0.0131 |                          0      |
| California           |              0.35   |                  0.3333 |               0.0574 |      0.6087 |          0.1304 |           0.0917 |                          0.3228 |
| Colorado             |              0      |                  0.0196 |               0.012  |      0.0217 |          0      |           0.0052 |                          0.0315 |
| Connecticut          |              0      |                  0      |               0.0191 |      0      |          0      |           0.0131 |                          0      |
| Delaware             |              0.0167 |                  0      |               0.0885 |      0      |          0      |           0.0026 |                          0.0079 |
| District of Columbia |              0.0167 |                  0      |               0      |      0      |          0      |           0.0118 |                          0      |
| Florida              |              0.0667 |                  0      |               0.0048 |      0      |          0.0435 |           0.0341 |                          0.0787 |
| Georgia              |              0      |                  0      |               0.0024 |      0      |          0      |           0.021  |                          0      |
| Hawaii               |              0      |                  0      |               0      |      0      |          0      |           0.0052 |                          0.0079 |
| Idaho                |              0      |                  0      |               0.012  |      0      |          0.0145 |           0      |                          0.0236 |
| Illinois             |              0.0167 |                  0.0196 |               0.0024 |      0      |          0.0145 |           0.055  |                          0      |
| Indiana              |              0.0333 |                  0.0196 |               0.0191 |      0.0652 |          0      |           0.0315 |                          0.0394 |
| Iowa                 |              0      |                  0      |               0.012  |      0      |          0      |           0.0039 |                          0      |
| Kansas               |              0      |                  0      |               0.0072 |      0      |          0.0145 |           0.0066 |                          0      |
| Kentucky             |              0.0167 |                  0.0392 |               0.0024 |      0      |          0      |           0.0118 |                          0      |
| Louisiana            |              0.05   |                  0.0196 |               0      |      0      |          0.2029 |           0.021  |                          0.0472 |
| Maine                |              0      |                  0.0196 |               0.0167 |      0.0217 |          0      |           0.0131 |                          0      |
| Maryland             |              0      |                  0      |               0.0598 |      0      |          0      |           0.0419 |                          0.0079 |
| Massachusetts        |              0      |                  0.0196 |               0.0191 |      0      |          0      |           0.0092 |                          0.0157 |
| Michigan             |              0.05   |                  0      |               0.0096 |      0.0217 |          0.0145 |           0.1088 |                          0.0236 |
| Minnesota            |              0      |                  0      |               0.0096 |      0      |          0      |           0.0144 |                          0      |
| Mississippi          |              0      |                  0      |               0.0072 |      0      |          0      |           0      |                          0.0079 |
| Missouri             |              0      |                  0      |               0.012  |      0      |          0      |           0.0144 |                          0.0079 |
| Montana              |              0      |                  0      |               0.0024 |      0.0435 |          0      |           0      |                          0      |
| Nebraska             |              0      |                  0      |               0      |      0      |          0.0145 |           0.0039 |                          0      |
| Nevada               |              0      |                  0      |               0.0167 |      0      |          0      |           0      |                          0      |
| New Hampshire        |              0      |                  0      |               0.0287 |      0      |          0      |           0.0026 |                          0      |
| New Jersey           |              0      |                  0      |               0.0239 |      0      |          0      |           0.0301 |                          0.0157 |
| New Mexico           |              0      |                  0.0196 |               0.0144 |      0      |          0      |           0      |                          0.0079 |
| New York             |              0.0333 |                  0.2353 |               0.0311 |      0      |          0.058  |           0.0433 |                          0.0551 |
| North Carolina       |              0      |                  0      |               0.0096 |      0      |          0      |           0.0406 |                          0.0394 |
| North Dakota         |              0      |                  0.0196 |               0      |      0      |          0.0145 |           0      |                          0      |
| Ohio                 |              0.0167 |                  0      |               0.0335 |      0      |          0      |           0.0341 |                          0.0157 |
| Oklahoma             |              0      |                  0      |               0.0072 |      0.0217 |          0.0435 |           0.0223 |                          0      |
| Oregon               |              0.0167 |                  0      |               0.0478 |      0      |          0      |           0.0066 |                          0      |
| Pennsylvania         |              0.0167 |                  0      |               0.0144 |      0      |          0      |           0.0629 |                          0.0157 |
| South Carolina       |              0      |                  0      |               0      |      0      |          0      |           0.0105 |                          0      |
| South Dakota         |              0      |                  0      |               0      |      0.0435 |          0      |           0      |                          0      |
| Tennessee            |              0.0333 |                  0      |               0.0191 |      0      |          0.0145 |           0.0262 |                          0.0236 |
| Texas                |              0.1    |                  0.1176 |               0.0311 |      0      |          0.2464 |           0.0852 |                          0.1575 |
| Utah                 |              0.0167 |                  0      |               0.0837 |      0      |          0.0145 |           0.0026 |                          0.0157 |
| Vermont              |              0      |                  0      |               0.0215 |      0      |          0      |           0      |                          0      |
| Virginia             |              0.0167 |                  0      |               0.0024 |      0      |          0.029  |           0.0419 |                          0.0079 |
| Washington           |              0.0167 |                  0.0196 |               0.1531 |      0.1087 |          0.0145 |           0.0315 |                          0.0079 |
| West Virginia        |              0      |                  0      |               0.0024 |      0      |          0      |           0.0039 |                          0      |
| Wisconsin            |              0      |                  0.098  |               0.0167 |      0      |          0.0145 |           0.0092 |                          0      |
| Wyoming              |              0.0167 |                  0      |               0.0072 |      0.0217 |          0      |           0.0013 |                          0      |

To more easily view the leading state for each cause of outage, we ran idxmax() on the DataFrame.

| CAUSE.CATEGORY                | 0          |
|:------------------------------|:-----------|
| equipment failure             | California |
| fuel supply emergency         | California |
| intentional attack            | Washington |
| islanding                     | California |
| public appeal                 | Texas      |
| severe weather                | Michigan   |
| system operability disruption | California |

As shown in the DataFrame, Washington was the leading state for the greatest proportion of outages caused by intentional attacks in all of United States. It was from this information we formulated our Hypothesis Question. We were curious to figure out if it was by chance alone that Washington (1st Place)along with Delaware (2nd Place) became the leaders of outages caused from intentional attacks.

## Assessment of Missingness

### NMAR Analysis
While it is required by law for electric companies to report outages, some private companies may withold the number of outages they report so as to downplay the severity of an outages or avoid negative publicity. As a result, the `OUTAGE.DURATION` might contain NMAR values. While this is only a theory, it's still a possibility considering that the data generating process are based off company made reports to the Federal Energy Regulatory Commission. To avoid negative publicity or fines, private companies may withold from informing certain outages.

### Missingness Dependency
**Dependency of OUTAGE.DURATION to CAUSE.CATEGORY**

Here, we measured the dependency of the missing values in the `OUTAGE.DURATION` column to `CAUSE.CATEGORY` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0.001 as p-value. Since p-value is below five percent, we conclude that the missing value in `OUTAGE.DURATION` column is highly dependent to `CAUSE.CATEGORY` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_Causality.html" width=700 height=500 frameBorder=0></iframe>

**Dependency of OUTAGE.DURATION to YEAR**

Here, we measured the dependency of the missing values in the `OUTAGE.DURATION` column to `YEAR` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0 as p-value. Since p-value is zero, we conclude that the missing value in `OUTAGE.DURATION` column is extremely dependent to `YEAR` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_Year.html" width=700 height=500 frameBorder=0></iframe>

**Independency of OUTAGE.DURATION to POSTAL.CODE**

Here, we measured the independency of the missing values in the `OUTAGE.DURATION` column to `POSTAL.CODE` column using 1000 times of permutation testing with Total Variation Distance as test statistics. As a result, we came up with 0.245 as p-value. Since p-value is above five percent, we conclude that the missing value in `OUTAGE.DURATION` column is independent to `POSTAL.CODE` column. 

The graph illustrates empirical distribution of TVD from the permutation testing. Red line represents the observed TVD. 

<iframe src="assets/TVD_Duration_State.html" width=700 height=500 frameBorder=0></iframe>

## Hypothesis Testing

As stated earlier, we discovered Washington was the leading state for the greatest proportion of outages caused by intentional attacks in all of United States. **We became curious to figure out if it was by random chance that Washington along with Delaware became the leaders of outages caused from intentional attacks.**

**Null Hypothesis**: In the population, the proportion of outages caused by intentional attacks are similar across all U.S. States, and the cause of proportionally higher cases of intentional attacks in certain U.S. States seen in our sample is due to random chance. 

**Alternative Hypothesis**: In the population, the reason for proportionally higher cases of intentional attacks in certian U.S. States cannot be explained by random chance alone. Something bigger is at play.  

**Relevant Columns**
The two main columns necessary to perform out Hypothesis Test is `POSTAL.CODE` and `OUTAGE.CAUSE`. Since we are performing the test under the null hypothesis, we must generate our test statistic from shuffling the `OUTAGE.CAUSE`.

**Test Statistic** 
After shuffling, we must label each outage as either an intentional attack or non-intentional attack. This allows us to use the **Total Variation Distance** as our test statistic which describes the distance between two categorical distributions.

Repeatedly computing the TVD of the shuffled `OUTAGE.CAUSE` will generate an empirical distribution of TVDs, under the null hypothesis. 


**Empirical Distribution of TVDs**
Red Line = Observed TVD

<iframe src="assets/TVD_Intentional.html" width=700 height=500 frameBorder=0></iframe>
The red line indicates p-value.

**Conclusion**
The chance that the observed TVD came from the distribution of TVDs under the null is essentially 0 because the **p-value = 0**. This confirms our speculation that it was not by chance that certain states experiences higher degrees of outages caused by intentional attacks. There must be some greater reason to why Washington or Delaware experiences more attacks on their power grids, whether it may be from groups targeting power grids due to political reasons or employees intentionally damage equipment to protest working conditions. Whatever the cause, our calculated p-value indicates strong evidence against the null hypothesis.