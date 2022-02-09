# Project 1: Query Project
## Part 1 and Part 2
*W205 | Section 5 | Lina Yang*
___
This markdown contains the following deliverables for Project 1: 

- [Part 1 - Querying Data with BigQuery](#Part-1)
- [Part 2 - Querying data from the BigQuery CLI](#Part-2)

For Part 3 of this project, click the below link: 

- [Part 3 - Employ notebooks to synthesize query project results](https://github.com/mids-w205-de-sola/project-1-linayang-mids/blob/assignment_1/Project_1.ipynb)
___

<a id='Part-1'></a> 
## Part 1: Querying Data with BigQuery
We will first answer six basic questions to get started. To answer these questions, we will use the Google BigQuery GUI interface to fetch the answers to these questions using SQL. 

### Some Initial Queries

#### 1. What's the size of this dataset? (i.e., how many trips)
**Answer**: The size of the data set is 983,648 rows, i.e. there are 983,648 trips. 

```sql
#standardSQL
SELECT
  COUNT(*) as total_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
```

#### 2. What is the earliest start date and time and latest end date and time for a trip?
**Answer**: The earliest start date and time is 2013-08-29 09:08:00. The latest end date and time is 2016-08-31 23:48:00. It looks like we have around three years of trip data.

```sql
#standardSQL
SELECT
  MIN(start_date) as earliest_start_date_time,
  MAX(end_date) as latest_end_date_time
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
```

#### 3. How many bikes are there?
**Answer**: Assuming that each dock is paired with a bike, I am going to count the number of docks there are to determine the number of bikes there are. There are 1,344 bikes.

```sql
#standardSQL
SELECT
  SUM(dockcount) AS total_bikes
FROM
  `bigquery-public-data.san_francisco.bikeshare_stations`
```

### Questions of my own
#### 4. How many bike stations are there? 
**Answer**: There are 74 stations.

```sql
#standardSQL
SELECT
  COUNT(station_id) AS total_stations
FROM
  `bigquery-public-data.san_francisco.bikeshare_stations`
```

#### 5. What is the total number of trips by the hour of day (using the start time)? When are the bikes used the most?
**Answer**: The table below shows the breakout by hour of day. In terms of trip volume, it looks like the busiest time of day for trips is 8am and 5pm with 132,464 and 126,302 trips, respectively. 

```sql
#standardSQL
SELECT
  EXTRACT(hour
  FROM
    CAST(start_date AS time)) AS hour,
  COUNT(trip_id) num_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
GROUP BY
  1
ORDER BY
  1
```

|hour of day|total_trips|
|---|---|
|0|2,929|
|1|1,611|
|2|877|
|3|605|
|4|1,398|
|5|5,098|
|6|20,519|
|7|67,531|
|8|132,464|
|9|96,118|
|10|42,782|
|11|40,407|
|12|46,950|
|13|43,714|
|14|37,852|
|15|47,626|
|16|88,755|
|17|126,302|
|18|84,569|
|19|41,071|
|20|22,747|
|21|15,258|
|22|10,270|
|23|6,195|

#### 6. Which subscriber type takes more trips?
**Answer**: Subscribers take more trips than Customers. In the entire data set, subscribers take 846,839, while customers take 136,809 trips. 

```sql
#standardSQL
SELECT
subscriber_type,
count(trip_id)
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
GROUP BY
  1
ORDER BY
  1
```
<a id='Part-2'></a> 
## Part 2: Querying data from the BigQuery CLI
Now we will show how to run queries directly in the Linux command line. Below include 4 initial queries. 

#### 1. What's the size of this dataset? (i.e., how many trips)
**Answer**: The size of the data set is 983,648 rows, i.e. there are 983,648 trips. 

```sql
bq query --use_legacy_sql=false '
SELECT
  COUNT(*) as total_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`'
```
##### Output:

|total_trips|
|-:|
|983648|

#### 2. What is the earliest start date and time and latest end date and time for a trip?
**Answer**: The earliest start date and time is 2013-08-29 09:08:00. The latest end date and time is 2016-08-31 23:48:00.

```sql
bq query --use_legacy_sql=false '
SELECT
  MIN(start_date) as earliest_start_date_time,
  MAX(end_date) as latest_end_date_time
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`'
```
##### Output:

| earliest_start_date_time | latest_end_date_time |
|-:|-:|
|      2013-08-29 09:08:00 |  2016-08-31 23:48:00 |

#### 3. How many bikes are there?
**Answer**: There are 1,344 bikes. 
```sql
bq query --use_legacy_sql=false '
SELECT
  SUM(dockcount) AS total_bikes
FROM
  `bigquery-public-data.san_francisco.bikeshare_stations`'
```
##### Output:

| total_bikes|
|:-|
|1344|

#### 4. How many trips are in the morning vs. in the afternoon?
**Answer**: The table below shows the breakout of trips by parts of day. According to [Merriam-Webster](https://www.learnersdictionary.com/qa/parts-of-the-day-early-morning-late-morning-etc), morning is from 5am-noon and afternoon is noon to 5pm. I have segmented the data as thus. There are 404,919 trips in the morning and 391,199 in the afternoon.

```sql
bq query --use_legacy_sql=false '
SELECT
  CASE
    WHEN EXTRACT(hour FROM CAST(start_date AS time)) in (5,6,7,8,9,10,11) THEN "morning"
    WHEN EXTRACT(hour FROM CAST(start_date AS time)) in(12,13,14,15,16,17) THEN "afternoon"
    WHEN EXTRACT(hour FROM CAST(start_date AS time)) in (18,19,20,21) THEN "evening"
    WHEN EXTRACT(hour FROM CAST(start_date AS time)) in (22,23,24,0,1,2,3,4) THEN "night"
    ELSE NULL
  END AS parts_of_day,
  count(trip_id) AS total_trips
FROM
    `bigquery-public-data.san_francisco.bikeshare_trips`
  GROUP BY 1'
```
##### Output:

| parts_of_day | total_trips |
|:-|:-|
| afternoon    |      391199 |
| evening      |      163645 |
| morning      |      404919 |
| night        |       23885 |


### Project Questions
Now that we've provided sample queries on how to run SQL queries in a Linux command line, below shows the project questions we want to answer in order to provide recommendations to our problem statement. We will go back to using the Google BigQuery GUI interface to fetch the answers to these questions. 

In making recommendations for offers and promotions, it is prudent to understand how bike riders are behaving and what potential revenue they are generating for the brand. With the limited data we on a customer level, the `subscriber_type` column feature will be an important indicator in helping us determine what is a commuter trip and how each type of customer segment is behaving. 

While the data set does not provide subscriber/customer level data on the initial flat rider/membership fee, we can estimate the amount of incremental revenue earned from extra charges due to additional minutes per ride. As we can see on the Bay Wheels [website](https://www.lyft.com/bikes/bay-wheels/pricing) regardless of type of membership a rider subscribes to they have a limited amount of time per ride before being charged a nominal fee for every additional minute they go over the base time. For Subscribers they are charged \\$0.20 for every additional minute after 45 mins and Customers are charged \\$0.30 for every additional minute after 30 mins. The below project questions will hone in on these exceeded minute fees and their relationship to rider behavior, and explore what makes a "commuter" trip. 

#### Question 1: What are the most popular start stations for subscribers and customers?
**Answer**: The most popular start stations for subscribers and customers all seem to be in the downtown area of San Francisco. 
```sql
#standardSQL
WITH
  top_5_customer_stations AS (
  SELECT
    *,
    ROW_NUMBER() OVER (ORDER BY total_trips DESC) row_num
  FROM (
    SELECT
      subscriber_type,
      start_station_id,
      start_station_name,
      COUNT(trip_id) AS total_trips
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips`
    WHERE
      subscriber_type = 'Customer'
    GROUP BY
      1,
      2,
      3 )
  LIMIT
    5 ),
top_5_subscriber_stations AS (
  SELECT
    *,
    ROW_NUMBER() OVER (ORDER BY total_trips DESC) row_num
  FROM (
    SELECT
      subscriber_type,
      start_station_id,
      start_station_name,
      COUNT(trip_id) AS total_trips
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips`
    WHERE
      subscriber_type = 'Subscriber'
    GROUP BY
      1,
      2,
      3 )
  LIMIT
    5 )
SELECT
  *
FROM
  top_5_customer_stations
UNION ALL
SELECT
  *
FROM
  top_5_subscriber_stations
```
**Output:** 

|subscriber_type|start_station_id|start_station_name|total_trips|row_num|
|:-|:-|:-|-:|-:|
|Subscriber |70 |San Francisco Caltrain (Townsend at 4th) |68,384 |1|
|Subscriber |69 |San Francisco Caltrain 2 (330 Townsend)  |53,694 |2|
|Subscriber |55 |Temporary Transbay Terminal (Howard at Beale)  |37,888 |3|
|Subscriber |50 |Harry Bridges Plaza (Ferry Building) |36,621 |4|
|Subscriber |61 |2nd at Townsend  |35,500 |5|
|Customer |60 |Embarcadero at Sansome |13,934 |1|
|Customer |50 |Harry Bridges Plaza (Ferry Building) |12,441 |2|
|Customer |76 |Market at 4th  |5,952  |3|
|Customer |39 |Powell Street BART |5,214  |4|
|Customer |48 |Embarcadero at Vallejo |4,945  |5|

#### Question 2: What is the most common routes for subscribers and customers? 
**Answer:** While the common routes are still within the downtown area for both Subscribers and Customers, we are starting to see that Customers are definitely having longer average trip duration than Subscribers.  
```sql
#standardSQL
WITH
  top_5_customer_routes AS (
  SELECT
    *,
    ROW_NUMBER() OVER (ORDER BY total_trips DESC) row_num
  FROM (
    SELECT
      subscriber_type,
      start_station_name,
      end_station_name,
      COUNT(trip_id) AS total_trips,
      AVG(duration_sec) AS avg_trip_duration,
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips`
    WHERE
      subscriber_type = 'Customer'
    GROUP BY
      subscriber_type,
      start_station_name,
      end_station_name
    ORDER BY
      total_trips DESC)
  LIMIT
    5 ),
  top_5_subscriber_routes AS (
  SELECT
    *,
    ROW_NUMBER() OVER (ORDER BY total_trips DESC) row_num
  FROM (
    SELECT
      subscriber_type,
      start_station_name,
      end_station_name,
      COUNT(trip_id) AS total_trips,
      AVG(duration_sec) AS avg_trip_duration,
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips`
    WHERE
      subscriber_type = 'Subscriber'
    GROUP BY
      subscriber_type,
      start_station_name,
      end_station_name )
  LIMIT
    5 )
SELECT
  *
FROM
  top_5_customer_routes
UNION ALL
SELECT
  *
FROM
  top_5_subscriber_routes
```
**Output:** 

|subscriber_type  |start_station_name |end_station_name |total_trips| avg_trip_duration  |row_num|
|:-|  :-|  :-|  -:|  -:|  :-|
|Subscriber |San Francisco Caltrain 2 (330 Townsend)  |Townsend at 7th  |8305 |296.668272125226 |1|
|Subscriber |2nd at Townsend  |Harry Bridges Plaza (Ferry Building) |6931 |516.79166065503  |2|
|Subscriber |Townsend at 7th  |San Francisco Caltrain 2 (330 Townsend)  |6641 |253.329167294082 |3|
|Subscriber |Harry Bridges Plaza (Ferry Building) |2nd at Townsend  |6332 |585.459570435881 |4|
|Subscriber |Embarcadero at Sansome |Steuart at Market  |6200 |399.368548387096 |5|
|Customer |Harry Bridges Plaza (Ferry Building) |Embarcadero at Sansome |3667 |2283.8549222798|1|
|Customer |Embarcadero at Sansome |Embarcadero at Sansome |2545 |4724.20667976424 |2|
|Customer |Harry Bridges Plaza (Ferry Building) |Harry Bridges Plaza (Ferry Building)  |2004 |6802.47704590819 |3|
|Customer |Embarcadero at Sansome |Harry Bridges Plaza (Ferry Building)|1638 |1679.1282051282  |4|
|Customer |Embarcadero at Vallejo |Embarcadero at Sansome|1345 |2675.30185873605 |5|

#### Question 3: What is the average time per ride for Subscribers and Customers?
**Answer**: Looking more into the average trip duration, we see that Customers' average trip duration is six times longer than a Subscribers' trip duration.  
```sql
#standardSQL
SELECT
  subscriber_type,
  MAX(duration_sec) AS max_trip_duration,
  ROUND(AVG(duration_sec),2) AS mean_trip_duration,
  MIN(duration_sec) AS min_trip_duration
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
GROUP BY
  1
```
**Output:** 

|subscriber_type  |max_trip_duration  |mean_trip_duration |min_trip_duration|
|:-|-:|-:|-:|
|Customer |17270400 |3718.79  |60|
|Subscriber |1852590  |582.76 |60|


#### Question 4: What is the total number of trips by the hour of day (using the start time) for Subscribers and Customers?
**Answer:** It looks like Customers across hours in the day stay relatively consistent, while Subscribers have two large spikes at 8am in the morning and 5pm in the evening which align with American rush hour traffic. These results are starting to indicate that Subscriber trips are more likely to be 'commuter' trips. 

```sql
SELECT
  *
FROM (
  SELECT
    subscriber_type,
    EXTRACT(hour
    FROM
      CAST(start_date AS time)) AS hour,
    COUNT(*) AS num_trips
  FROM
    `bigquery-public-data.san_francisco.bikeshare_trips`
  GROUP BY
    subscriber_type,
    hour ) PIVOT ( SUM(num_trips) AS total_trips FOR subscriber_type IN ('Customer',
      'Subscriber') )
ORDER BY
  hour 
```
**Output:** 

|hour |total_trips_Customer |total_trips_Subscriber|
|:-|-:|-:|
|0  |848  |2081|
|1  |639  |972|
|2  |413  |464|
|3  |208  |397|
|4  |160  |1238|
|5  |303  |4795|
|6  |945  |19574|
|7  |2585 |64946|
|8  |5293 |127171|
|9  |6572 |89546|
|10 |8250 |34532|
|11 |11078  |29329|
|12 |12508  |34442|
|13 |12719  |30995|
|14 |12737  |25115|
|15 |12806  |34820|
|16 |12704  |76051|
|17 |11387  |114915|
|18 |8771 |75798|
|19 |5556 |35515|
|20 |3741 |19006|
|21 |2808 |12450|
|22 |2285 |7985|
|23 |1493 |4702|

#### Question 5: What is the total number of trips by the day of week (using the start time) for Subscribers and Customers?
**Answer:** Subscribers are most likely to take trips Monday through Friday, while Customers flat line across all days with very slight lifts on Sunday and Saturday. Further indicating that Subscribers are more likely to be commuters.  

```sql
SELECT
  CASE
    WHEN day_of_week = 1 THEN 'sunday'
    WHEN day_of_week = 2 THEN 'monday'
    WHEN day_of_week = 3 THEN 'tuesday'
    WHEN day_of_week = 4 THEN 'wednesday'
    WHEN day_of_week = 5 THEN 'thursday'
    WHEN day_of_week = 6 THEN 'friday'
    WHEN day_of_week = 7 THEN 'saturday'
END
  AS day_name,
  *
FROM (
  SELECT
    subscriber_type,
    EXTRACT(DAYOFWEEK
    FROM
      CAST(start_date AS DATE)) AS day_of_week,
    COUNT(*) AS num_trips
  FROM
    `bigquery-public-data.san_francisco.bikeshare_trips`
  GROUP BY
    subscriber_type,
    day_of_week ) PIVOT ( SUM(num_trips) AS total_trips FOR subscriber_type IN ('Customer',
      'Subscriber') )
ORDER BY
  DAY_OF_WEEK
```
**Output:** 

|day_name |day_of_week  |total_trips_Customer |total_trips_Subscriber|
|:-|:-|-:|-:|
|sunday |1  |25908  |25467|
|monday |2  |15142  |154795|
|tuesday  |3  |14737  |169668|
|wednesday  |4  |15237  |165530|
|thursday |5  |16612  |160296|
|friday |6  |19929  |140048|
|saturday |7  |29244  |31035|

#### Question 6: What is the month over month growth in total trips for Subscribers and Customers?
**Answer**: As indicated earlier in Part 1, in the data set from 2013 to 2016, Subscribers are taking more trips than Customers. This also rings true with month over month growth. Since 2013 Subscribers have an average month over month growth of 47% while Customers have an average 21% growth. 
```sql
#standardSQL
WITH
  base_table AS (
  SELECT
    *
  FROM (
    SELECT
      DATE_TRUNC(start_date, month) AS month_start,
      subscriber_type,
      COUNT(*) AS num_trips
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips`
    GROUP BY
      1,
      2) PIVOT ( SUM(num_trips) AS total_trips FOR subscriber_type IN ('Customer',
        'Subscriber') )
  ORDER BY
    month_start ),
  mom_growth AS (
  SELECT
    month_start,
    total_trips_Customer,
    (total_trips_Customer - LAG(total_trips_Customer, 1) OVER (ORDER BY month_start)) / LAG(total_trips_Customer, 1) OVER (ORDER BY month_start) AS customer_growth,
    total_trips_Subscriber,
    (total_trips_Subscriber - LAG(total_trips_Subscriber, 1) OVER (ORDER BY month_start)) / LAG(total_trips_Subscriber, 1) OVER (ORDER BY month_start) AS subscriber_growth
  FROM
    base_table
  ORDER BY
    month_start )
SELECT
  100 * ROUND(AVG(customer_growth),2) || '%' AS avg_customer_growth,
  100 * ROUND(AVG(subscriber_growth),2) || '%' AS avg_subscriber_growth
FROM
  mom_growth ;
```
**Output:** 

|avg_customer_growth  |avg_subscriber_growth|
|:-|:-|
|21% |47%|

#### Question 7: Are Subscribers more likely to exceed their ride time than Customers?
Under the [help portal](https://help.baywheels.com/hc/en-us/categories/360002170651-How-do-you-want-to-ride-) of Bay Wheels, we can see that we currently charge additional minutes for each ride based on Subscriber or Customer. For Subscriber we give riders 45-minute rides and then charge an additional \\$0.20 per minute and for Customers we give riders 30-minute rides and then charge an additional \\$0.30 per minute. It would be prudent for us to see if Subscribers or Customers are exceeding their ride limit. This may provide some indication whether there is any price sensitive behavior.     

Interestingly, we see that 30% of all trips taken by Customers exceeded their initial 30 mins, while less than 1% of Subscribers exceed their initial 45 mins. 

```sql
#standardSQL
WITH
  base_table AS (
  SELECT
    trip_id,
    subscriber_type,
    start_station_name,
    end_station_name,
    duration_sec,
    CASE
      WHEN subscriber_type= 'Subscriber' AND duration_sec > 2700 THEN 'exceeded'
      WHEN subscriber_type='Customer'
    AND duration_sec >1800 THEN 'exceeded'
    ELSE
    'under'
  END
    AS exceeded_min_flag
  FROM
    `bigquery-public-data.san_francisco.bikeshare_trips` )
SELECT
  subscriber_type,
  COUNT(trip_id) total_trips,
  SUM(CASE
      WHEN exceeded_min_flag = 'exceeded' THEN 1
  END
    ) AS trips_exceeded_mins,
  ROUND(SUM(CASE
        WHEN exceeded_min_flag = 'exceeded' THEN 1
    END
      )/COUNT(trip_id),4) AS pct_exceeded
FROM
  base_table
GROUP BY
  1 ;
```
**Output:** 

|subscriber_type  |total_trips  |trips_exceeded_mins  |pct_exceeded|
|:-|:-|:-|-:|
|Customer |136809 |42013  |0.3071|
|Subscriber |846839 |2926 |0.0035|

#### Question 8: What is the *estimated* incremental revenue that Bay Wheels generates from exceeded ride time? 

In the table schema for `bikeshare_trips` table, they define the two different subscriber_type dimensions we have available to us in the data set. Subscriber = annual or 30-day member, Customer = 24-hour or 3-day member. While due to data privacy, we cannot identify any customer-level detail to estimate overall membership fees. We can certainly estimate the incremental revenue gained from exceeded ride time. ased on the Bay Wheels website, there doesn't seem to be a 3-day member option anymore, so we will generalize that the exceeded ride time fee is the same as the Access Pass. For Subscriber we give riders 45-minute rides and then charge an additional \\$0.20 per minute and for Customers we give riders 30-minute rides and then charge an additional \\$0.30 per minute. 

Using this arithmetic, we are seeing Customers between 2013 and 2016 generated almost \\$1.2 million in exceeded minutes fees while Subscribers only generated \\$210K in exceeded minute fees. 

```sql
#standardSQL
SELECT
  subscriber_type,
  ROUND(SUM(exceeded_min_rev),2) total_exceeded_min_rev
  FROM (
    SELECT
      trip_id,
      subscriber_type,
      start_station_name,
      end_station_name,
      duration_sec,
      CASE
        WHEN subscriber_type= 'Subscriber' AND duration_sec > 2700 THEN (duration_sec-2700)/60*0.30
        WHEN subscriber_type='Customer'
      AND duration_sec >1800 THEN (duration_sec-1800)/60*0.20
      ELSE
      NULL
    END
      AS exceeded_min_rev
    FROM
      `bigquery-public-data.san_francisco.bikeshare_trips` )
  GROUP BY
    1
```
**Output:** 

|subscriber_type  |total_rev_exceeded_min|
|:-|-:|
|Customer |1,167,138.45|
|Subscriber |210,032.59|

### Insights
Subscribers are using the Bay Wheels bike share program more frequently and during 2013 and 2016 seeing higher average month over month growth than Customers. They are only really using the bikes during the work week and seeing the highest peaks in bike riding between 6 to 10 am and 3 to 7pm. However, Subscribers do seem to be price-sensitive or at least cognizant of their trip duration. They are less likely to exceed their 45 min ride compared to Customers. While Customers are not seeing as much growth month over month, they have generated almost sixe times more incremental revenue from exceeded minute fees. Continue to this [Jupyter Notebook](https://github.com/mids-w205-de-sola/project-1-linayang-mids/blob/assignment_1/Project_1.ipynb) to review a few visualizations and our recommendations and conclusions. 