# ETL-off-a-SQS-Queue

```
Author: Debanjali Saha
Email: debsa2000@gmail.com

```
I have used Python to complete this exercise.

### Overview

* This data pipeline is made to run in an isolated container in which it polls messages from a standard AWS SQS queue in batches of 10 messages per iteration.
* In each iteration we extract the message body -> parse JSON data into a dataframe -> mask IP and Device ID fields with SHA256 hash and store the updated dataframe in PostgreSQL user_logins table in Postgresql db.
* After a batch of messages is processed and stored in the database, we delete them from the queue and poll for the next batch of messages.
* We stop the process after all the messages in the queue have been processed successfully.

Answers to questions while developing the solution:
 ● How will you read messages from the queue?
 ● What type of data structures should be used?
 ● How will you mask the PII data so that duplicate values can be identified?
 ● What will be your strategy for connecting and writing to Postgres?
 ● Where and how will your application run?


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

* Open Postgresql in a new shell and check if the data has been populated in the table
```
 $ psql -d postgres -U postgres -p 5432 -h localhost -W
postgres=# select * from user_logins; 
```

### Questions

● How would you deploy this application in production?
- Set up a production-ready infrastructure using AWS services, such as SQS and RDS for Postgres.
- Create separate environments for development, testing, and production.
- Use containerization tools like Docker and container orchestration platforms like Kubernetes to manage and deploy your application.
- Package the application using Docker and deploy it over a cluster of machines or Cloud environment in production. Monitor it continuously to ensure it runs smoothly, and automate and schedule the pipeline to run at regular intervals using a cron job.

● What other components would you want to add to make this production ready?
- Implement logging and monitoring using tools like AWS CloudWatch or ELK stack.
- Add error handling and retry mechanisms for handling transient failures.
- Implement security measures to protect sensitive data and secure database connections.
- Set up automated tests and CI/CD pipelines.
- Implement scalability and load balancing to handle growing datasets and increased traffic.
- Data backup and recovery to ensure that valuable data is not lost.

● How can this application scale with a growing dataset?
- Utilize AWS services that can handle larger workloads, such as Amazon SQS for scalable message queuing and Amazon RDS for managed PostgreSQL database.
- Employ techniques like sharding and partitioning to distribute the data across multiple database instances.
- Optimize database queries and ensure proper indexing to maintain performance as the dataset grows.
- Parallel processing or Asynchronous handling of each message from the queue to scale with increasing data volume.
- Distributed processing using Big Data tools such as Spark, HDFS, etc. 

● How can PII be recovered later on?
- To recover the original PII from the masked values, the mapping between the original values needs to be stored and their masked counterparts need to be stored in a separate secure location. This mapping should only be accessible to authorized individuals and should follow proper data protection protocols.
- The other way is to use reversible hashing techniques or token/key-based hashing techniques.

● What are the assumptions you made?
- Device ID and IP fields are never null.
- Allowing duplicates assuming there is another downstream process responsible for data cleaning.
- The data received from the SQS queue is valid JSON and follows the expected structure.
- The provided table schema is correct, and the necessary table already exists in the Postgres database.
- The masking algorithm and approach mentioned (consistent hash function) are acceptable for the purpose of this exercise.
- The provided Docker images with test data are set up correctly and can be used without modification.


### Next Steps

* This application can be further organized into a structured data pipeline using Airflow so that dependencies can be managed properly between different services.
* Changing the current SHA256 hashing to A reversible hashing technique to recover the masked PII.
* Data can also be stored as Documents in a NoSQL database like MongoDB so that we can directly store source JSON data in its true form.
* Data visualization tools such as PowerBI, Tableau, etc can be used on top of stored data for analysis.
* Splunk can also be used to index the data stored in documents/NoSQL db for interactive and rich Data visualization dashboards.
