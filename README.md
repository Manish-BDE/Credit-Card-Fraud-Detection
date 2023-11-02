# Credit-Card-Fraud-Detection

1. PROBLEM STATEMENT:

With the increasing digitalization and online transactions, it becomes ever so important for credit card companies to be able to recognize "genuine" and "fraudulent" transactions in order to provide their customers with a more secure and seamless experience. As a big data engineer, you should architect and design a solution using all the technologies learned during this program to meet the following requirements:

• Detect fraudulent transactions in the shortest possible time (Since the transactions are happening in real-time, timing constraints play a very important role). Whenever a card member swipes his/her card for payment, the transaction should be classified as fraudulent /authentic based on a set of predefined rules.

• To resolve customer complaints and queries, the support team should be made available with the latest customer details (by constantly keeping them updated).

SOLUTION ARCHITECTURE:
STEP 1: 
  • Copy “card_transactions.csv” file from local system to HDFS. (a)Table creation steps :

STEP 2:
  • Create the “card_transactions” table in MySQL based on the card_transactions.csv file structure.

STEP 3: 
  • Do a sqoop export to the database for card_transactions.csv and delete the file from HDFS.

STEP 4: 
  • On “member_score” and “member_details” create a normal hive external table.

STEP 5: 
  • Create a special “card_transactions” Hbase table managed by Hive.

STEP 6: 
  • Create a Hbase “lookup” table with columns - member_id, card_id, UCL, timestamp, zipcode, credit_score.

(b)Batch Processing steps:

STEP 7: 
  • Sqoop import member_score from AWS-RDS to Hive. (Full load import, has to be refreshed every week)

STEP 8: 
  • Sqoop import member_details from AWS-RDS to Hive. (Incremental load import in append mode based on member_id for every 8hrs)

STEP 9: 
  • Sqoop import card_transactions to HDFS from MySQL. (This is a one-time full load activity. The card_transactions table will be updated with new transactions while in streaming mode.)

(c)Scheduling STEPs:

STEP 10: 
  • Schedule a sqoop import job using Airflow to import member_score from AWS-RDS to Hive on a full-load.

STEP 11: 
  • Schedule a sqoop import job using Airflow to import member_details from AWS-RDS to Hive on an incremental append mode for every 8hrs.

(c)Integration STEPs: 
STEP 12: 
  • Spark-HBase Integration
  • For populating the card_transactions table. 
  • For populating the look_up table.

STEP 13: 
  • Spark-Hive Integration for spark stream processing.

STEP 14: 
  • Access the hive tables using apache spark and calculate the UCL.

(d)Streaming STEPs: 
STEP 15: 
  • Producer to create the transactions in JSON format, to be added and queued in Kafka topics.

STEP 16: 
  • Spark structured streaming program as a Consumer that will consume the data from kafka topics.

STEP 17: 
  • Retrieve the timestamp and zipcode of the last transaction of each card.

STEP 18: 
  • Processing in Spark Streaming -

STEP 19.1 : Validating RULE 1 -> “credit_score > 200”

STEP 19.2 : Validating RULE 2 -> “transaction amount <= UCL”

STEP 19.3 : Validating RULE 3 -> “zipcode distance within threshold”

STEP 19: 
  • Based on the above rules, the entire transaction along with status should be updated in the card_transactions table.

STEP 20: 
  • Schedule a job for validating rules by comparing the incoming data from the POS terminals in JSON format with the values in the lookup table.

STEP 21: 
  • If the transaction was marked genuine, then we need to update the lookup table with the new timestamp and the zipcode.

STEP 22: 
  • Schedule a job for populating the lookup table.
