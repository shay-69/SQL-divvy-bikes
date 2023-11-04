# SQL-divvy-bikes
An exploration of 4m rows of Divvy Bikes data using SQL.

# I want to look at my data, so let's select all.
select *
from public."202201_divvy_tripdata" dt;

# In total, these are our columns:
> ride_id (varchar 20)
> rideable_type (varchar 50)
> started_at (varchar 50)
> ended_at (varchar 50)
> start_station_name (varchar 250)
> start_station_id (varchar 50)
> end_station_name (varchar 250)
> end_station_id (varchar 50)
> start_lat (float4)
> start_lng (float4)
> end_lat (float4)
> end_lng (float4)
> member_casual (varchar 50)
# Let's explore!

# There are four MILLION rows in this dataset, but how many months of data do we have?
# And are some months more popular than others for bike rental?
# I organise them by month, to see if we have a full year, and total rides, to see if there is a pattern.
select date_part('month',started_at::date) as month, count(*)
from public."202201_divvy_tripdata" dt 
group by month
order by month;

# The months go from 1 (January) to 9 (September) 2023, with June being the most popular month at 823, 488 rides.
# January is the least popular with only (!) 103k rides.
# There's definitely a seasonality to bike popularity even throughout the warmer months!
# Next, let's see the difference between how many riders were paying members vs casual users.
select date_part('month',started_at::date) as month, count(*), member_casual as members
from public."202201_divvy_tripdata" dt 
where member_casual = 'member'
group by member_casual, month
order by month

# Very interesting. Out of 103, 770 riders in January, 85, 250 were members.
# Perhaps members are using the bikes to commute.
# I want to know the most popular stations people are starting and ending at, whichever way they're going.
# What are the top 15 most popular routes (station name combination)?
select start_station_name, end_station_name, count(*) as combination_count
from public."202201_divvy_tripdata" dt 
where start_station_name <> end_station_name 
group by start_station_name, end_station_name
order by combination_count desc 
limit 15;

# Woah! Looks like the most popular route is [blank] to [blank]. Not helpful!
# There are a lot of blanks in this dataset. We'll have to filter them out.
select start_station_name, end_station_name, count(*) as combination_count
from public."202201_divvy_tripdata" dt 
where start_station_name <> '' and end_station_name <> '' 
	and start_station_name <> end_station_name 
group by start_station_name, end_station_name
order by combination_count desc 
limit 15;

# Adding [start_station_name <> ''] means that the blank values now disappear.
# It looks like the cells aren't empty - they just have a blank space in them.
# That's why IS NOT NULL didn't work!

# And with the combination_count, we can see how popular these routes have been.
# The most popular route is DuSable Lake Shore Dr & Monroe St <> Streeter Dr & Grand Ave, with 4, 787 rides.

# Thanks for reading!











