# An Analysis of Major Power Outages
*Govind Sriram* 
---
## Introduction
In this analysis, I work with a data set of major power outages from January 2000 to July 2016, as compiled by the [Purdue Lab for Advancing Sustainable Critical Infrastructure](https://engineering.purdue.edu/LASCI/research-data/outages). It includes 57 columns of observations/data that describe the Location, Duration, Cause, Weather Effects, Effect Size, Land Coverage, and Energy Commodity Prices among other features of a typical power outage. There are 1534 rows of such observations.


First, I will process and clean the data, selecting features that I believe will most relevant to my analysis. This will give me a baseline for the underlying trends in the data. Then, I will determine missingness mechanisms in the dataset - this might be helpful should I choose to impute values.

I will then investigate my research problem: How can we predict the cause of an outage? What features are most important to making this prediction? I will attempt to build a model that predicts the cause of an outage. This is important, because it can help utility companies ancitapate the cause of an outage without sending in field workers, thereby saving time and leading to a potentially faster outage resolution. It can also allow utility companies to plan infrastructure upgrades to mitigate any future outages.

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

*Table 1.1: Cleaned Data*

|    |   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   TOTAL.PRICE |   CUSTOMERS.AFFECTED |   ANOMALY.LEVEL |   DEMAND.LOSS.MW |   POPDEN_URBAN |   TOTAL.CUSTOMERS |   TOTAL.SALES |   COM.PERCEN |   IND.PERCEN | OUTAGE.START        | OUTAGE.RESTORATION   |
|---|-------|--------|-------------|--------------|--------------|-------------------|-------------------|-------------------|------------------------|------------------|--------------|---------------------|----------------|-----------------|---------------|------------------|--------------|-------------|-------------|--------------------|---------------------|
|  0 |   2011 |       7 | Minnesota    | MN            | MRO           | East North Central | normal             | severe weather     | nan                     |              3060 |          9.28 |                70000 |            -0.3 |              nan |           2279 |           2595696 |   6.56252e+06 |      32.225  |      32.2024 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|  1 |   2014 |       5 | Minnesota    | MN            | MRO           | East North Central | normal             | intentional attack | vandalism               |                 1 |          9.28 |                  nan |            -0.1 |              nan |           2279 |           2640737 |   5.28423e+06 |      34.2104 |      35.7276 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|  2 |   2010 |      10 | Minnesota    | MN            | MRO           | East North Central | cold               | severe weather     | heavy wind              |              3000 |          8.15 |                70000 |            -1.5 |              nan |           2279 |           2586905 |   5.22212e+06 |      34.501  |      37.366  | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|  3 |   2012 |       6 | Minnesota    | MN            | MRO           | East North Central | normal             | severe weather     | thunderstorm            |              2550 |          9.19 |                68200 |            -0.1 |              nan |           2279 |           2606813 |   5.78706e+06 |      33.5433 |      34.4393 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|  4 |   2015 |       7 | Minnesota    | MN            | MRO           | East North Central | warm               | severe weather     | nan                     |              1740 |         10.43 |               250000 |             1.2 |              250 |           2279 |           2673531 |   5.97034e+06 |      36.2059 |      29.7795 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |
### Univariate Analysis
I then perform some analysis to see how single variables are distributed.

- I first see how outages are distributed by Climate Region
    - It seems that most outages occur in the Northeast Climate Region.
<iframe
  src="assets/uni1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

- I also see how outages occur over time. There is a very visible spike at the year 2011, with 269 major outages.

<iframe
  src="assets/uni2.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

### Bivariate Analysis
We look at some interesting interactions between different features in the data.

- First, I look at the interaction between Outage Duration and Peak Demand loss 
    - The "clumping" behavior in the scatter plot is characteristic of many bivariate plots from this data.
<iframe
  src="assets/bi1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

- I then create a new feature "IS.MORNING" based on the time of day when an Outage occurs, and graph distributions of outage category conditional on this feature.
- The conditional distributions seem to be relatively similar - I might want to run a test to check that.
<iframe
  src="assets/bi2.html"
  width="800"
  height="500"
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
- **Null:** The distribution of Cause Category is the same when `DEMAND.LOSS.MW` is missing, and when it is not.
- **Alternate:** The distribution of Cause Category is different when `DEMAND.LOSS.MW` is missing, and when it is not.

- Here is the distribution as visualized from the dataset
<iframe
  src="assets/missing_1_base.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

- Here is the empirical distribution of TVDs. The Observed value is indicated by the red line. 
<iframe
  src="assets/missing_1_perm.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

In this test, the p-value was 0.0, much smaller than our cutoff - we reject the null hypothesis. We have evidence to believe that the distribution of Cause Category when Demand Loss is missing is significantly different from when it is not missing.
#### `CLIMATE.CATEGORY`
- **Null:** The distribution of Climate Category is the same when `DEMAND.LOSS.MW` is missing, and when it is not.
- **Alternate:** The distribution of Climate Category is different when `DEMAND.LOSS.MW` is missing, and when it is not.

- Here is the distribution as visualized from the dataset

<iframe
  src="assets/missing_2_base.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

- Here is the empirical distribution of TVDs. The Observed value is indicated by the red line. 
<iframe
  src="assets/missing_2_perm.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

In this test, the p-value (0.5324) was greater than our cutoff - we fail to reject the null hypothesis. We do not have conclusive evidence to believe that the distribution of Cause Category when Demand Loss is missing is significantly different from when it is not missing.

## Hypothesis Testing
I will be testing whether the distribution of `CAUSE.CATEGORY` when `IS.MORNING` is `True` is different when when it is not. To do quantify this difference, I will be using a TVD for this permutation test.
The TVD is a non-directional test statistic that is of use to our test because we are dealing with categorical distributions. My p-value cutoff will be `0.05`.

- **Null:** The distribution of Cause Category is the same when `IS.MORNING` is `True`, and when it is `False`.
- **Alternate:** The distribution of Cause Category differs when `IS.MORNING` is `True`, and when it is `False`.

- Here is the empirical distribution of TVDs. The red line indicates the observed value from the data.
<iframe
  src="assets/hyp_all.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The resulting p-value was `0.0` after 10,000 iterations, meaning that with our significance level of 0.05, we reject the null hypothesis in favor the alternate because we have sufficient evidence to believe that the distribution of Cause Category differs when `IS.MORNING` is `True`, and when it is `False`.
## A Prediction Problem
**Prediction Problem:** Predict the cause of an Outage.
This is a multiclass classification problem.

For this model, I will be using `U.S_STATE`(categorical), `CLIMATE.REGION` (categorical), `ANOMALY.LEVEL` (quantitative), `POPDEN_URBAN`(quantitative), and `MONTH` (ordinal). These columns represent the data we know at the time of prediction - they will not change as the outage progresses, not will they be immediately be affected in the event of an outage.

I will also be including two engineered features:
- `COMIND.PERCEN`, the combined commercial and industrial percentage of consumers (quantitative)
- `ABS.ANOMALY`, the absolute value of the anomaly level (quantitative)
These features rely on internal data that electricity companies know, as they provide service in the areas where outages happen. Again, we will know this information prior to prediction because these features can be derived from existing company data, as well as live government sources.

I will be predicting `CAUSE.CATEGORY`.
I will be using the F1 score to evaluate my model, because it is most appropriate for a classifier. Using the F1 score will help us manage out class imbalance (as found in our univariate analysis), and provide us with a way to aggregate precision and recall metrics.
## Baseline Model
For my Baseline model, I will be using Random Forest Model to classify an outage's cause. I will be using the features `U.S_STATE`(categorical), `CLIMATE.REGION` (categorical), `ANOMALY.LEVEL` (quantitative), `MONTH` (ordinal), and`POPDEN_URBAN`(quantitative). These were chosen because `U.S_STATE` accounts for variances between states, `CLIMATE.REGION` indicates whether the cause could be man-made or natural. `ANOMALY.LEVEL` accounts for anomalous conditions that may have a one-off influence, and `POPDEN_URBAN` could provide supplementary information - more densely populated regions may have a higher occurence of intentional attacks.
`MONTH` is already encoded as numerical data. I one-hot encode all of the categorical data, and scale the numerical data. While not necessary, I do it because it may help interpretability of the model in the future.

This model achieved an F1 Score of 0.5750, with an accuracy of 0.59 on the test set. This is quite decent for a baseline model. 

## Final Model
My Final model includes all features from the baseline model, plus `ABS.ANOMALY` and `COMIND.PERCEN` (both are quantitative features). I chose to engineer `ABS.ANOMALY` because I realized that the anomaly level really only defines the "strangeness" of the weather by its magnititude - values greater than 0.5 and lesser than -0.5 are considered an anomaly. This could help the model by providing a proxy for more data. `COMIND.PERCEN` was engineered to represent the relative industrial density in an area - people tend to live in areas with more favorable climates, while industries require large amounts of space in more rural settings, where conditions may be more dire.

I performed grid search to find optimal parameters, and my hyperparameters were:
```
{
'classifier__criterion': 'entropy',
'classifier__max_depth': 50,
'classifier__min_samples_split': 5, 
'classifier__n_estimators': 200
}
```
My final model's testing F1 score was 0.6178, a 7.4% improvement over the baseline. While not a substantial increase, I found this difference to be consistent across multiple train-test splits. Not a great model, but not bad either. The accuracy also increased similarly, to 0.64. 
I chose not to "roll" for a better score because that would bias us towards the testing dataset.

## Fairness
I chose the groups for the fairness analysis to be urban density - High Density versus Low Density. The goal is to see whether there is any significant difference between the F1 scores when my model predicts on High-Density versus Low-Density areas. High-Density is characterized as having a `POPDEN.URBAN` greater than 2000.
  
- **Null:** F1 Scores when outages occur in High-Density areas versus Low-Density areas are roughly the same, and minute variations occur due to random chance.
- **Alternate:** F1 Scores when outages occur in High-Density areas versus Low-Density areas are different, and are not caused by random variation.

I will be using the absolute difference in F1 Scores as my test statistic in this permutation test.
My significance level will be 0.05. I performed 10000 iterations of a permutation test, and my p-value was very large (0.425). I fail to reject the null - there is no significant difference between the F1 scores for High and Low Density areas.
<iframe
  src="assets/fairness.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
