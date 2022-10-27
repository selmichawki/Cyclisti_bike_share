
# Google Data Analytics - Case Study
This case study was developed using the Google data analytics framework: Ask, Prepare, Process, Analyze, Share and Act.
The tools used to complete it were Excel, Microsoft SQL Server, and R. This unique assemble of tools and techniques allows businesses to really embrace data in their day-to-day decisions.

## The Study Case
Cyclists is a fictional company in Chicago that owns a bike-share type of business. They have two types of customers, one is a Subscriber that holds an annual pass and the other is a casual rider that holds an hour or day pass. Their goal and expectations are to increase the number of Subscribers in order to increase overall profit. The dataset provided is the result of one-year transactions. For this dataset Subscriber = 'Subscriber' and Casual Riders = 'Customer'
## Ask
Ask is the art of asking the right questions and as many of them as possible in order to understand the problem and level the expectation of the stakeholders. 
Deliverable: A clear statement of the business task
How do annual members and casual riders use Cyclistic bikes?
## Prepare
This phase allows the analyst to verify if the data is reliable, Is there a pipeline established by the data engineering team that ensures data quality and data integrity? Does the available data answer the business question or more data is needed?

Deliverables: 
For this case study, it was assumed that the data is sourced from a reliable database
There are at least 4 features that could be used to answer the business question.

Installing pachakges
```r
library(tidyverse)
library(lubridate)
library(ggplot2)
library(janitor)

```
## Collecting data 

```r
sep_22_trip_data <- read_csv("202209-divvy-tripdata.csv")
aug_22_trip_data <- read_csv("202208-divvy-tripdata.csv")
jul_22_trip_data <- read_csv("202207-divvy-tripdata.csv")
jun_22_trip_data <- read_csv("202206-divvy-tripdata.csv")
may_22_trip_data <- read_csv("202205-divvy-tripdata.csv")
apr_22_trip_data <- read_csv("202204-divvy-tripdata.csv")
mar_22_trip_data <- read_csv("202203-divvy-tripdata.csv")
feb_22_trip_data <- read_csv("202202-divvy-tripdata.csv")
jan_22_trip_data <- read_csv("202201-divvy-tripdata.csv")
dec_21_trip_data <- read_csv("202112-divvy-tripdata.csv"
nov_21_trip_data <- read_csv("202111-divvy-tripdata.csv")
oct_21_trip_data <- read_csv("202110-divvy-tripdata.csv")
```

## Wrangling data 

```r
colnames(sep_22_trip_data)
colnames(aug_22_trip_data)
colnames(jul_22_trip_data)
colnames(jun_22_trip_data)
colnames(may_22_trip_data)
colnames(apr_22_trip_data)
colnames(mar_22_trip_data)
colnames(feb_22_trip_data)
colnames(jan_22_trip_data)
colnames(dec_21_trip_data)
colnames(nov_21_trip_data)
colnames(oct_21_trip_data)

```
Compare column datatype across dataframes to check for error

```r
compare_df_cols(sep_22_trip_data, aug_22_trip_data, jul_22_trip_data,
jun_22_trip_data, may_22_trip_data, apr_22_trip_data, mar_22_trip_data,
feb_22_trip_data, jan_22_trip_data, dec_21_trip_data, nov_21_trip_data,
oct_21_trip_data, return = "mismatch")

```
Stack individual months’s data frames into one big data frame

```r
all_trips <- bind_rows(sep_22_trip_data, aug_22_trip_data, jul_22_trip_data,
jun_22_trip_data, may_22_trip_data, apr_22_trip_data, mar_22_trip_data,
feb_22_trip_data, jan_22_trip_data, dec_21_trip_data, nov_21_trip_data,
oct_21_trip_data)


```









