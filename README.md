# ETL-off-a-SQS-Queue

```
Author: Debanjali Saha
Email: debsa2000@gmail.com

```

### Overview

* This data pipeline is made to run in an isolated container in which it polls messages from a standard AWS SQS queue in bathces of 10 messages per iteration.
* In each iteration we extract the message body -> parse json data into a dataframe -> mask IP and Device ID fields with SHA256 hash and store the updated dataframe in postgresql user_logins table in postgresql db.
* After a batch of messages are processed and stored in db, we delete them from the queue and poll for the next batch of messages.
* We stop the process after all the messages in the queue have been processed successfully.
* Below is the basic workflow diagram of the entire pipeline.

<img src="https://user-images.githubusercontent.com/32909781/216669908-9522ee92-8f24-476c-bc9d-938920c7972d.png">

