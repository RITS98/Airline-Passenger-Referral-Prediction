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


### Create Visual ETL
1. Add the source and create a filter to extract recors where delay is greater than and equal to 60 minutes
<img width="920" alt="image" src="https://github.com/user-attachments/assets/1c59165e-ffed-4034-a27b-905b1468aa4f">

2. Create a join with dimension table in Redshift to get the departure airports from the ```airports_dim``` table
<img width="818" alt="image" src="https://github.com/user-attachments/assets/dd4243b9-2a1a-4261-a409-4c0a62c3d906">

3. Change Change Schema step to change column names and drop columns not present in the table of ```daily_flight_fact``` as given in (3c)
<img width="892" alt="image" src="https://github.com/user-attachments/assets/251d9c24-3a25-422f-acc5-da7b54fd903b">

4. Create another join with dimension table in Redshift to get the arrival airports from the ```airports_dim``` table
<img width="848" alt="image" src="https://github.com/user-attachments/assets/f1808da3-eb04-44d0-941f-ca76bd828f1e">

5. Create another Change Schema step to modify column names and drop columns
<img width="857" alt="image" src="https://github.com/user-attachments/assets/1345ac6f-a768-4316-a05f-ab4d5d681d2f">

6. Add a target step (AWS Redshhift to Ingest the processed data) into the facts table
<img width="334" alt="image" src="https://github.com/user-attachments/assets/c14dbbe7-fae0-414b-973c-dd80d7db1b2b">


### Create Step Function
1. In the step function, choose Start Crawler step to start the Glue Crawler. <br>
2. Then implement GetCrawler step to get the status of the Glue Crawler.<br>
3. in the Choice State, if the Crawler is running, Then Wait Else Go to next step. <br>
<img width="944" alt="image" src="https://github.com/user-attachments/assets/16d97c29-0983-4640-b480-b8d4bce476ba">

5. If Execution is completed, then 











