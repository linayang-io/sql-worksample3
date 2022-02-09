# Project 1: Query Project
*W205 | Section 5 | Lina Yang*
___

## Problem Statement
Lyft Bay Wheels, formerly known as Ford GoBike, is a bicycle sharing system in the San Francisco Bay Area. It serves communities across San Francisco, East Bay and San Jose. The service offers a lot of different options ranging from single ride to annual memberships. Understanding what kind of offers are most popular and drive the most revenue for Bay Wheels is a valuable indicator on understanding who Lyft Bay Wheels's customers are and what initiatives Bay Wheels can implement to continue its success. 

Through this analysis, we will provide recommendations on what kind of offer deals Lyft Bay Wheels's marketing team should activate in order to increase ridership and profit. Additionally, we will focus in on a specific customer segment, "Commuters" to provide insight into what are the 5 most popular trips for commuters, i.e. commuter trips.

The main questions we would like to answer are: 
- What are the 5 most popular trips that you would call "commuter trips"?
- What are your recommendations for offers (justify based on your findings)?

## Data Source & Tools
We will be using a public dataset from the Bay Bike Share. There are three data tables we will use under this public dataset:
- **bikeshare_stations**: contains an index of stations where where bikeshare users can pick up or return bikes.  
- **bikeshare_status**: contains the number of bikes and docks available at each station per minute. 
- **bikeshare_trips**: contains data on individual bike trips including duration of ride, start date/time and end date/time, and start and end station. 

The public dataset is available through Google BigQuery. We will be using the Google BigQuery GUI interface to fetch the data using SQL. We will also show how to query the data from the BigQuery CLI.

We will then use Pandas to format output tables and Python plotting libraries to create simple visualizations to justify our findings and recommendations. 

## Table of Contents

Below are links to parts of project: 

- [Part 1 - Querying Data with BigQuery](https://github.com/mids-w205-de-sola/project-1-linayang-mids/blob/assignment_1/Project_1_Part_1_2.md#Part-1)
- [Part 2 - Querying data from the BigQuery CLI](https://github.com/mids-w205-de-sola/project-1-linayang-mids/blob/assignment_1/Project_1_Part_1_2.md#Part-2)
- [Part 3 - Employ notebooks to synthesize query project results](https://github.com/mids-w205-de-sola/project-1-linayang-mids/blob/assignment_1/Project_1.ipynb)

## Conclusion & Recommendations
**What are the 5 most popular trips that you would call "commuter trips"?**

We have defined commuter trips as trips that belonged to Subscribers who have started their trips between 6am-10am and 3-7pm. We found that the 5 most popular trips for commuter trips were: 

- San Francisco Caltrain 2 (330 Townsend) -> Townsend at 7th
- 2nd at Townsend -> Harry Bridges Plaza (Ferry Building)
- Embarcadero at Folsom -> San Francisco Caltrain (Townsend at 4th)
- Harry Bridges Plaza (Ferry Building) -> 2nd at Townsend
- Temporary Transbay Terminal (Howard at Beale) -> San Francisco Caltrain (Townsend at 4th)

**What are your recommendations for offers (justify based on your findings)?**

With any promotional/offer analysis, we need to better understand how it affects Bay Wheel's revenue. While we do not have access to Subscriber/Customer level data to estimate retention or membership fees, we do have the ability to estimate the exceeded time limit fee a trip generates. Bay Wheels offers bike riders a time limit for each ride, and if exceeded we charge them a nominal fee for additional minutes incurred. For Subscriber, we give riders 45-minute rides and then charge an additional \\$0.20 per minute and for Customers we give riders 30-minute rides and then charge an additional \\$0.30 per minute. Additional information on pricing can be found [here](https://www.lyft.com/bikes/bay-wheels/pricing). 

We see later in the analysis that while the fee is nominal, with almost one million trips taken during 2013-2016, the money generated from this offering is actually quite significant. We also see year-over-year, the revenue generated from this fee is reducing, which provides Bay Wheels an opportunity right now to test pricing to see if this provides more revenue in the future. Customers are more likely to exceed their time limit per ride and historically has generated more money through these fees for Bay Wheels. This may connote that Customers are more likely to be enjoying their bike ride, taking longer rides, and maybe even less price-sensitive. We also see that when we measure the average revenue over hours of the day, all riders seem to be generating higher exceeded time limit fees for Bay Wheels during the late evening which typically is synonymous with night life and leisure activities. It may connote that bikers are less price-sensitive during these times and are less focused on their bike's time limit. As they say, time flies when you are having fun.

I recommend increasing the exceeded time limit fee to \\$0.30 for Subscribers and \\$0.35 for Customers and test this to see if it changes the demand or behavior of riders. Additionally, dynamically increasing the exceeded time limit fee in the late evening for both Subscribers and Customers would also help generate some additional revenue as this behavior seems to be more frequent during these hours. Also, in turn by increasing the exceeded time limit fee during these hours, this might help with having bikes returned on time allowing our team to be more productive with maintaining stations or bikes during these hours.