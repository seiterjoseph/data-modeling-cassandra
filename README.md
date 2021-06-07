
<center><img src="images/cas.png" style="width: 300px;"/></center>
<h1 align="center">Data Modeling with Cassandra</h1>


## Summaryy

A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

They'd like a data engineer to create an Apache Cassandra database which can create queries on song play data to answer the questions, and wish to bring you on the project. Your role is to create a database for this analysis. You'll be able to test your database by running queries given to you by the analytics team from Sparkify to create the results.

## Project Description

In this project, we will model the data with Apache Cassandra and complete an ETL pipeline using Python. Tables in Apache Cassandra to run queries are created and the ETL pipeline that transfers data from a set of CSV files within a directory are defined to create a streamlined CSV file to model and insert data into Apache Cassandra tables.



## Dataset
For this project, we'll be working with one dataset: `event_data`. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:

```
event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv
```
The image below is a screenshot of what the denormalized data should appear like in the `event_datafile_new.csv`:

![erd](./images/image_event_datafile_new.jpg)



## Project Steps


**Modeling NoSQL database or Apache Cassandra database**

1. Design tables to answer the queries outlined in the project
2. Write Apache Cassandra `CREATE KEYSPACE` and `SET KEYSPACE` statements
3. Develop `CREATE` statement for each of the tables to address each question
4. Load the data with `INSERT` statement for each of the tables
5. Include `IF NOT EXISTS` clauses in the `CREATE` statements to create tables only if the tables do not already exist. Then include `DROP TABLE` statement for each table, to run drop and create tables whenever necessary to reset the  database and test the ETL pipeline
6. Test by running the proper select statements with the correct `WHERE` clause



## Query Answers to the Questions

#### Question 1
> Give me the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4


In this query, I used `sessionId` as the **partition key** and `itemInSession` as my **clustering key**. Each partition is uniquely identified by sessionId while itemInSession was used to uniquely identify the rows within a partition to sort the data by the value of 4 .

```    
query = query + "(sessionId int, itemInSession int, /
                  artist text, song text, length float, /
                  PRIMARY KEY (sessionId, itemInSession))"
```
Output:
```
Faithless Music Matters (Mark Knight Dub) 495.30731201171875
```


#### Question 2
> Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182


In this query, I used `userId` and `sessionId` as my **composite partition keys** and `itemInSession` as my **clustering key**. Each partition is uniquely identified by the composite partition keys and itemInSession was used to uniquely identify the rows within partitions to sort the data. From there the clustering column will sort in order of how they were added to the primary key and in this case rows are ordered by the itemInSession column.

```    
query = query + "(userid int, sessionId int, itemInSession int, /
                  artist text, song text, firstName text, lastName text, /
                  PRIMARY KEY ((userId, sessionId), itemInSession))"

```
Output:
```
Down To The Bone Keep On Keepin' On Sylvie Cruz
Three Drives Greece 2000 Sylvie Cruz
Sebastien Tellier Kilometer Sylvie Cruz
Lonnie Gordon Catch You Baby (Steve Pitron & Max Sanna Radio Edit) Sylvie Cruz
```

#### Question 3
> Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'


In this query, I used `song` as my **partition key** and `userId` as the **clustering key**. The keys song, and userId are combined to form a unique hash to identify each record (row). This is done since many users could have the same first name and last name and might have listened to the same song (i.e song = 'All Hands Against His Own').

```    
query = query + "(song text, userId int, firstName text, lastName text, /
                  PRIMARY KEY (song, userId))"

```
Output:
```
Jacqueline Lynch
Tegan Levine
Sara Johnson
```
