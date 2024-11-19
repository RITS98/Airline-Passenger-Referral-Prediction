# Airline-Passenger-Referral-Prediction

### Create S3 bucket and folders
1. Created a S3 bucket named ```data-airline-landing-zn```
<img width="1053" alt="image" src="https://github.com/user-attachments/assets/b10fe8f3-509a-45fc-bdaf-96977fb3311f">

2. Inside the bucket there are two folders namely ```daily-flights``` and ```dim```
<img width="1365" alt="image" src="https://github.com/user-attachments/assets/57b79d98-8cf2-4a4a-ae6a-e9eb7e417553">

3. Uploaded a ```airports.csv``` file to ```dim``` folder. It is a slow changing dimension table containing the names of airports, city, state, and id.
<img width="490" alt="image" src="https://github.com/user-attachments/assets/8ebae536-f49f-4564-8b35-47bf6264862f">

### Create Redshift database (For Data Warehouse)
1. Created a Redshift cluster
<img width="1580" alt="image" src="https://github.com/user-attachments/assets/6bf60a2d-926e-4451-b0a5-772f6f323f90">

2. Open the query editor

3. Create the schema and tables
  a. Create schema<br>
      ```
      create schema airlines;
      ```<br>
      
  b. Create a airports dimension table<br>
      ```
      CREATE TABLE airlines.airports_dim (
          airport_id BIGINT,
          city VARCHAR(100),
          state VARCHAR(100),
          name VARCHAR(200)
      );
      ```<br>
      
  c. Create a daily flights fact table<br>
     ```
     CREATE TABLE airlines.daily_flights_fact (
          carrier VARCHAR(10),
          dep_airport VARCHAR(200),
          arr_airport VARCHAR(200),
          dep_city VARCHAR(100),
          arr_city VARCHAR(100),
          dep_state VARCHAR(100),
          arr_state VARCHAR(100),
          dep_delay BIGINT,
          arr_delay BIGINT
      );
     ```<br>
     
  d. Write a copy command to copy the dimension data from S3 to Redshift Cluster<br>
     ```
      COPY airlines.airports_dim
        FROM <S3 bucket>
        IAM_ROLE <IAM S3 Read Only Role ARN>
        DELIMITER ','
        IGNOREHEADER 1
        REGION <Region>
     ```<br>

4. Create Read Only Access to S3 for Redshift Cluster
<img width="1246" alt="image" src="https://github.com/user-attachments/assets/79c27da3-6a09-4e30-9ffe-63947fdfb844">

5. Load data From S3 to Red Shift using the above mentioned COPY command
<img width="1468" alt="image" src="https://github.com/user-attachments/assets/84f3dc0a-90ad-4d60-8c9f-c2e00b58758a">

6. Create Endpoint for access to Redshift by Glue Crawler
Since the Redshift server is sitting inside the VPC we need to create a endpoint to access it.
<img width="1319" alt="image" src="https://github.com/user-attachments/assets/a2c7036c-aac6-4013-b60a-7fe73c8e167b">

### Create Glue Service

1. Create a Glue database to store metadata from Redshift cluster
<img width="929" alt="image" src="https://github.com/user-attachments/assets/1ef873f0-0cd3-42a8-b659-f38b51bad919">

2. Create Glue Crawler
<img width="849" alt="image" src="https://github.com/user-attachments/assets/62b88ef6-c8cc-4ccc-8268-8ea3e335072f">

3. Upon running the crawlers, the tables get loaded
<img width="638" alt="image" src="https://github.com/user-attachments/assets/4d101122-86d4-475e-8098-d4542ae20d0b">








