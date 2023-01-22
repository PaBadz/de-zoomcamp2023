## Week 1 Homework

In this homework we'll prepare the environment 
and practice with Docker and SQL


## Question 1. Knowing docker tags

Run the command to get information on Docker 

```docker --help```

Now run the command to get help on the "docker build" command

Which tag has the following text? - *Write the image ID to the file* 

--iidfile string          Write the image ID to the file

## Question 2. Understanding docker first run 

Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash.
Now check the python modules that are installed ( use pip list). 
How many python packages/modules are installed?


- 3
pip, setuptools, wheels

# Prepare Postgres

Run Postgres and load data as shown in the videos
We'll use the green taxi trips from January 2019:

```wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-01.csv.gz```

You will also need the dataset with zones:

```wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv```


```bash
docker-compose up -d
```

Code:
```bash
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-01.csv.gz"

python ingest_data.py \
  --user=root \
  --password=root \
  --host=localhost \
  --port=5432 \
  --db=ny_taxi \
  --table_name=green_taxi_trips \
  --url=${URL}
```

Zone:

```bash
URL="https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv"

python ingest_data_zone.py \
  --user=root \
  --password=root \
  --host=localhost \
  --port=5432 \
  --db=ny_taxi \
  --table_name=zone \
  --url=${URL}
```




## Question 3. Count records 

How many taxi trips were totally made on January 15?

Tip: started and finished on 2019-01-15. 

Remember that `lpep_pickup_datetime` and `lpep_dropoff_datetime` columns are in the format timestamp (date and hour+min+sec) and not in date.

select COUNT(*)
From green_taxi_trips
WHERE CAST(lpep_pickup_datetime AS DATE) = '2019-01-15' AND CAST(lpep_dropoff_datetime AS DATE) ='2019-01-15' 



- 20530

## Question 4. Largest trip for each day

Which was the day with the largest trip distance
Use the pick up time for your calculations.

select CAST(lpep_pickup_datetime AS DATE)
From green_taxi_trips
WHERE trip_distance in  (SELECT MAX(trip_distance) FROM green_taxi_trips)


- 2019-01-15

## Question 5. The number of passengers

In 2019-01-01 how many trips had 2 and 3 passengers?

select passenger_count,COUNT(*)
From green_taxi_trips
WHERE CAST(lpep_pickup_datetime AS DATE) = '2019-01-01'
AND passenger_count in  (2,3)
GROUP BY passenger_count
 

- 2: 1282 ; 3: 254



## Question 6. Largest tip

For the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip?
We want the name of the zone, not the id.

Note: it's not a typo, it's `tip` , not `trip`


SELECT zdo."Zone"
FROM green_taxi_trips g JOIN zone zdo ON g."DOLocationID" = zdo."LocationID"
JOIN zone zpu ON g."PULocationID" = zpu."LocationID"
WHERE zpu."Zone" = 'Astoria'
ORDER BY tip_amount DESC
LIMIT 1


- Long Island City/Queens Plaza


## Submitting the solutions

* Form for submitting: [form](https://forms.gle/EjphSkR1b3nsdojv7)
* You can submit your homework multiple times. In this case, only the last submission will be used. 

Deadline: 26 January (Thursday), 22:00 CET


## Solution

We will publish the solution here