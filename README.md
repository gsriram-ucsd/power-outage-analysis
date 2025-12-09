# An Analysis of Major Power Outages
*Govind Sriram* 
---
## Introduction
In this analysis, I work with a data set of major power outages from January 2000 to July 2016, as compiled by the [Purdue Lab for Advancing Sustainable Critical Infrastructure](https://engineering.purdue.edu/LASCI/research-data/outages). It includes 57 columns of observations/data that describe the Location, Duration, Cause, Weather Effects, Effect Size, Land Coverage, and Energy Commodity Prices among other features of a typical power outage. There are 1534 rows of such observations.


First, I will process and clean the data, selecting features that I believe will most relevant to my analysis. This will give me a baseline for the underlying trends in the data. Then, I will determine missingness mechanisms in the dataset - this might be helpful should I choose to impute values.

I will then investigate my research problem: How can we classify how long it will take for an outage to be resolved, and what factors are most important to making this prediction? I will attempt to build a model that predicts whether an outage is Extended or Rapid in its resolution. This is important, because it can help consumers anticipate the rate of recovery for outages, and because it can help utility companies understand what influences the recovery time. This will allow them to allocate resources efficiently. It can also allow utility companies to plan infrastructure upgrades to mitigate any future outages.

I will be working with a subset of the columns provided, mainly because they will be of value to my analysis and the process of answering my research question.

*Table 1: Data Description of relevant columns* 

| **Column Name** | **Description** |
| --------------- | ---------------  |
| YEAR | Year of outage |
| MONTH | Month at time of outage |
| U.S._STATE | State covered by the outage (Continental U.S) |
| POSTAL.CODE | Postal Code of the U.S State (2-Letter Code) |
| NERC.REGION | (NERC) North American Electricity Reliability Corporation regions spanned by the outage |
| CLIMATE.REGION | U.S Climate Region |
| ANOMALY.LEVEL | El Nino/La Nina index (3-month running average) |
| CLIMATE.CATEGORY | Climate episodes as defined by ANOMALY.LEVEL  |
| OUTAGE.START.DATE | Day of the outage, as reported |
| OUTAGE.START.TIME | Time of the outage, as reported |
| OUTAGE.RESTORATION.DATE | Date when outage was resolved |
| OUTAGE.RESTORATION.TIME | Time when outage was resolved |
| CAUSE.CATEGORY | Overarching cause event for the outage |
| CAUSE.CATEGORY.DETAIL | Specific description as to cause of outage |
| OUTAGE.DURATION | Duration of outage (in minutes) |
| DEMAND.LOSS.MW | Peak Demand lost during an outage event  |
| CUSTOMERS.AFFECTED | Number of customer affected by outage |
| TOTAL.PRICE |  Average monthly price in the U.S State |
| POPDEN_URBAN | Urban Population Density |
| TOTAL.CUSTOMERS | Total number of customers served by the utility |
| TOTAL.SALES | Total Electricity consumption in the U.S State |
| COM.PERCEN | Percentage of commercial consumers |
| IND.PERCEN | Percentage of industrial consumers |

## Data Cleaning and Exploratory Data Analysis
### Cleaning
1. I start by dropping all features that I am not interested - I keep the features `YEAR `, `MONTH `, `U.S._STATE `, `POSTAL.CODE `, `NERC.REGION `, `CLIMATE.REGION `, `ANOMALY.LEVEL `, `CLIMATE.CATEGORY `, `OUTAGE.START.DATE `, `OUTAGE.START.TIME `, `OUTAGE.RESTORATION.DATE `, `OUTAGE.RESTORATION.TIME `, `CAUSE.CATEGORY `, `CAUSE.CATEGORY.DETAIL `, `OUTAGE.DURATION `, `DEMAND.LOSS.MW `, `CUSTOMERS.AFFECTED `, `TOTAL.PRICE `, `POPDEN_URBAN `, `TOTAL.CUSTOMERS `, `TOTAL.SALES `, `COM.PERCEN `, and `IND.PERCEN `.

2. I next combine `OUTAGE.START.TIME` and `OUTAGE.START.DATE` into one column consisting of Timestamps. I do the same for `OUTAGE.RESTORATION.TIME` and `OUTAGE.RESTORATION.DATE`

3. I then look for `DEMAND.LOSS.MW` values that are equal to zero, replacing them with `np.nan`. This is because we expect any outage to lose *some* amount of peak demand, even if for an instant. Other features, such as `CUSTOMERS.AFFECTED` and `OUTAGE.DURATION` are not modified this way, because it is possible for electricy companies to have backup systems, which would mean that no customers would be affected, with a resolution time of `0` minutes.

### Univariate Analysis
I then perform some analysis to see how single variables are distributed.

- I first see how outages are distributed by Climate Region
<iframe
  src="assets/uni1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- I also see how outages occur over time. There is a very visible spike at the year 2011, with 269 major outages.

<iframe
  src="assets/uni2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
We look at some interesting interactions between different features in the data.

- First, I look at the interaction between Outage Duration and Peak Demand loss 
    - The "clumping" behavior in the scatter plot is characteristic of many bivariate plots from this data.
<iframe
  src="assets/bi1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- I then create a new feature "IS.MORNING" based on the time of day when an Outage occurs, and graph distributions of outage category conditional on this feature.
<iframe
  src="assets/bi2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Pivots and Aggregates
I wanted to see how different types of Causes were distributed over the different regions of the U.S. Interestingly, the Northwest and the Suthwest have high percentages of outages caused by intentional attacks.


*Table 2: Distribution of Cause Modes by U.S Region* 

| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
| -------------------| --------------------| ------------------------| ---------------------| ------------| ----------------| -----------------| --------------------------------|
| Central            |                0.03 |                    0.02 |                 0.18 |        0.02 |            0.01 |             0.70 |                            0.05 |
| East North Central |                0.02 |                    0.03 |                 0.15 |        0.01 |            0.01 |             0.76 |                            0.02 |
| Northeast          |                0.01 |                    0.04 |                 0.38 |        0.00 |            0.01 |             0.51 |                            0.04 |
| Northwest          |                0.02 |                    0.01 |                 0.70 |        0.02 |            0.02 |             0.20 |                            0.03 |
| South              |                0.04 |                    0.02 |                 0.13 |        0.01 |            0.19 |             0.49 |                            0.12 |
| Southeast          |                0.03 |                    0.00 |                 0.06 |        0 00 |            0.03 |             0.77 |                            0.11 |
| Southwest          |                0.06 |                    0.01 |                 0.69 |        0.01 |            0.01 |             0.11 |                            0.10 |
| West               |                0.10 |                    0.05 |                 0.15 |        0.14 |            0.04 |             0.33 |                            0.19 |
| West North Central |                0.06 |                    0.00 |                 0.25 |        0.31 |            0.12 |             0.25 |                            0.00 |

## Analysis of Missingness
### NMAR Analysis 
Looking at the data, it would be prudent to conclude that `OUTAGE.DURATION` is NMAR, simply because the dataset is an aggregation of data, which could mean that some electricity companies may not record data that corresponds to `OUTAGE.DURATION`. If they did not report that value, then the values would be missing.

To determine if the data is MAR, I would need to recover or collect the details of the reporting electricity company for each row in the dataset, and perform a hypothesis test to determine if missingness of `OUTAGE.DURATION` is dependent on the electricity company.
### Missingness Dependency
I will focus on the distribution of `DEMAND.LOSS.MW`. I will first test this against `CAUSE.CATEGORY`, and then, `CLIMATE.CATEGORY`. I will use a p-value cutoff of `0.05`
#### `CAUSE.CATEGORY`
**Null:** The distribution of Cause Category is the same when `DEMAND.LOSS.MW` is missing, and when it is not.
**Alternate:** The distribution of Cause Category is different when `DEMAND.LOSS.MW` is missing, and when it is not.

- Here is the distribution as visualized from the dataset
<iframe
  src="assets/missing_1_base.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- Here is the empirical distribution of TVDs. The Observed value is indicated by the red line. 
<iframe
  src="assets/missing_1_perm.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In this test, the p-value was very small, much smaller than our cutoff - we reject the null hypothesis.

<iframe
  src="assets/missing_2_base.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/missing_2_perm.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


<iframe
  src="assets/hyp_all.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/fairness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
