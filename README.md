# An Analysis of Major Power Outages
*Govind Sriram* 
---
## Introduction
In this analysis, I work with a data set of major power outages from January 2000 to July 2016, as compiled by the [Purdue Lab for Advancing Sustainable Critical Infrastructure](https://engineering.purdue.edu/LASCI/research-data/outages). It includes 57 columns of observations/data that describe the Location, Duration, Cause, Weather Effects, Effect Size, Land Coverage, and Energy Commodity Prices among other features of a typical power outage. There are 1534 rows of such observations.


First, I will process and clean the data, selecting features that I believe will most relevant to my analysis. This will give me a baseline for the underlying trends in the data. Then, I will determine missingness mechanisms in the dataset - this might be helpful should I choose to impute values.

I will then investigate my research problem: How can we classify how long it will take for an outage to be resolved, and what factors are most important to making this prediction? I will attempt to build a model that predicts whether an outage is Extended or Rapid in its resolution. This is important, because it can help consumers anticipate the rate of recovery for outages, and because it can help utility companies understand what influences the recovery time. This will allow them to allocate resources efficiently. It can also allow utility companies to plan infrastructure upgrades to mitigate any future outages.

I will be working with a subset of the columns provided, mainly because they will be of value to my analysis and the process of answering my research question.

*Fig. 1: Data Description of relevant columns* 

| Column Name | Description |
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

<iframe
  src="assets/uni1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/uni2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/bi1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/bi2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/missing_1_base.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/missing_1_perm.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

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
