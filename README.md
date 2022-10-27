
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
## Data cleaning and preparing 
Let's see the list of columns and data types
```r
str(all_trips)
```
Summary of data 
```r
summary(all_trips)
```
Add columns that list the date, month, year and time of each rid
```r
all_trips$date <- as.Date(all_trips$started_at)
all_trips$month <- floor_date(all_trips$date, "month")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
all_trips$time <- format(as.POSIXct(all_trips$started_at), format = "%H:%M")

```
Add column that categorizes ride start time into time periods (Morning/Afternoon/Evening/Night)
```r
all_trips <- all_trips %>%
mutate(time_hour = hour(strptime(time, format = "%H:%M"))) %>%
mutate(time_period = case_when(time_hour >= 6 & time_hour <
12 ~ "Morning", time_hour >= 12 & time_hour < 18 ~ "Afternoon",
time_hour >= 18 & time_hour < 23 ~ "Evening", time_hour >=
23 | time_hour <= 6 ~ "Night"))
```
Add a “ride_length” calculation to all_trips (in seconds)
```r
all_trips$ride_length <- difftime(all_trips$ended_at, all_trips$started_at)
```
Convert “ride_length” from Factor to numeric so we can run calculations on the data

```r
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
```
Remove “bad” data as the dataframe contains ride_length that was negative or equal to 0
```r
all_trips <- subset(all_trips, ride_length > 0)
```
Change “ride_length units to minutes
```r
all_trips$ride_length <- all_trips$ride_length/60
```
Trim dataset by removing station name, lat, long from data set
```r
all_trips_trim <- all_trips %>%
select(-c(started_at, ended_at, start_station_name, start_station_id,
end_station_name, end_station_id, start_lat, start_lng,
end_lat, end_lng))

```

## Analyze
Descriptive analysis on ride_length (all figures in seconds)

```r
summary(all_trips_trim$ride_length)
```
Compare members and casual users 
#### MEAN
```r
aggregate(all_trips_trim$ride_length ~ all_trips_trim$member_casual,
FUN = mean)
```
#### MEDIAN
```r
aggregate(all_trips_trim$ride_length ~ all_trips_trim$member_casual,
FUN = median)

```
#### MAX
```r
aggregate(all_trips_trim$ride_length ~ all_trips_trim$member_casual,
FUN = max)


```
#### MIN
```r
aggregate(all_trips_trim$ride_length ~ all_trips_trim$member_casual,
FUN = min)

```
See the average ride time by each day for members vs casual users
```r
all_trips_trim$day_of_week <- ordered(all_trips_trim$day_of_week,
levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday",
"Friday", "Saturday"))
aggregate(all_trips_trim$ride_length ~ all_trips_trim$member_casual +
all_trips_trim$day_of_week, FUN = mean)


```
analyze ridership data by type and days of week

```r
all_trips_trim %>%
group_by(member_casual, day_of_week) %>%
summarize(number_of_rides = n(), average_duration = mean(ride_length)) %>%
arrange(member_casual, day_of_week)

```
visualize the average number of rides by rider type

```r
all_trips_trim %>%
   group_by(member_casual, day_of_week) %>%
   summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>%
   arrange(member_casual, day_of_week) %>%
   ggplot(aes(x = day_of_week, y = average_duration, fill = member_casual)) +
   geom_col(position = "dodge")
```

## EXPORT SUMMARY FILE FOR FURTHER ANALYSIS

Create table of the total number of departures of casual members from station names with
lat-lon data station IDs
```r
station_list_by_membertype <- all_trips %>%
   group_by(start_station_name, member_casual) %>%
   summarize(lat = round(mean(start_lat), 3), lon = round(mean(start_lng),3), num_departures =      n_distinct(ride_id), avg_ride = mean(ride_length))
```
Create table of summary data aggregated by days
```r
daily_trips <- all_trips_trim %>%
  group_by(date, time_period, member_casual, day_of_week, time_hour,rideable_type) %>%
  summarize(mean = mean(ride_length), sum = sum(ride_length))
```

Export csv files
```r
write.csv(station_list_by_membertype, file = "~/Desktop/station_list_by_membertype.csv")
write.csv(daily_trips, file = "~/Desktop/daily_trips.csv")
```













