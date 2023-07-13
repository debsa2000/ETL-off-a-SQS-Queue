# ETL-off-a-SQS-Queue

```
Author: Debanjali Saha
Email: debsa2000@gmail.com

```

### Overview

* This data pipeline is made to run in an isolated container in which it polls messages from a standard AWS SQS queue in batches of 10 messages per iteration.
* In each iteration we extract the message body -> parse JSON data into a dataframe -> mask IP and Device ID fields with SHA256 hash and store the updated dataframe in PostgreSQL user_logins table in Postgresql db.
* After a batch of messages is processed and stored in the db, we delete them from the queue and poll for the next batch of messages.
* We stop the process after all the messages in the queue have been processed successfully.


### Workflow diagram

<img src="https://user-images.githubusercontent.com/32909781/216669908-9522ee92-8f24-476c-bc9d-938920c7972d.png">

### How to Run
#### Pre-requisites
* [install docker](https://docs.docker.com/get-docker/)
* [install postgresql](https://www.postgresql.org/download/)

#### Steps to run the pipeline:
* Check if you have docker-compose installed
```
 $ docker-compose --version
```

* Run LocalStack and Postgresql containers
```
 $ docker-compose up --detach
```

* Build and run docker image for data-pipeline
```
 $ docker build -t debanjalisaha/fetch_data_pipeline .
 $ docker run debanjalisaha/fetch_data_pipeline
```

* Open Postgresql in new shell and check if the data has been populated in the table
```
 $ psql -d postgres -U postgres -p 5432 -h localhost -W
 psql>$ select * from user_logins; 
```

