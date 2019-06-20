# Data Engineer Nanodegree

## Data Modeling

## Project: Data Modeling with Apache Cassandra

## [Blog Version]()

## Table of Contents

- [Introduction](#intro)
- [Description](#describe)
- [Data](#data)
- [Part I: ETL Pipeline for Pre-Processing](#pre-etl)
- [Part II: Apache Cassandra Coding Portion](#cassandra-code)
  - [Creating a Cluster](#cluster-create)
  - [Create Keyspace](#keyspace-create)
  - [Set Keyspace](#keyspace-set)
- [Data Modeling](#data-modeling)
- [Files](#files)
- [Software Requirements](#sw_reqs)

***

<a id="intro"></a>

## Introduction

A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

They'd like a data engineer to create an **Apache Cassandra** database which can create queries on song play data to answer the questions. Our role is to create a database for this analysis. We'll be able to test the database by running queries given by the analytics team at Sparkify.

<a id="describe"></a>

## Description

In this project, we'll apply the concepts learned in data modeling with Apache Cassandra and complete an ETL pipeline using Python. I will model the data by creating tables in Apache Cassandra to run queries. We are provided with part of the ETL pipeline that transfers data from a set of CSV files within a directory to create a streamlined CSV file to model and insert data into Apache Cassandra tables.

<a id="data"></a>

## Data

We'll be working on one dataset: *event_data*. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:

```sh
event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv
```

This directory is same as the *log_data* directory for this [project](https://github.com/sanjeevai/data-modeling-postgres/). You can [view](https://github.com/sanjeevai/data-modeling-postgres/tree/master/data/log_data/2018/11) it for more information.

<a id="pre-etl"></a>

## Part I: ETL Pipeline for Pre-Processing

Data currently resides in a directory of JSON files. We need to create a streamlined CSV file from all these. The final file will be used to extract and insert data into Apache Cassandra tables.

The code to pre-process the CSV files was provided already. So no need to go in-depth for it.

This is what the file *event_datafile_new.csv* looks like. It has 6821 rows and contains the following columns.

- artist 
- firstName of user
- gender of user
- item number in session
- last name of user
- length of the song
- level (paid or free song)
- location of the user
- sessionId
- song title
- userId

![image_event_datafile_new](./img/image_event_datafile_new.jpg)

<a id="cassandra-code"></a>

## Part II: Apache Cassandra Coding Portion

We will model our data based on the queries provided to us by the analytics team at Sparkify. But first, let's setup Apache Cassandra for this. This is a three step process:

<a id="cluster-create"></a>

### Create a Cluster

We create a cluster and connect it to our local host. This makes a connection to a Cassandra instance on our local machine.

```python

from cassandra.cluster import Cluster
cluster = Cluster(['127.0.0.1'])

# To establish connection and begin executing queries, need a session
session = cluster.connect()
```

<a id="keyspace-create"></a>

### Create a Keyspace

```python
session.execute("""
CREATE KEYSPACE IF NOT EXISTS udacity
WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1}"""
)
```

This is similar to creating a database in Postgres where we specify the host and user privileges.

<a id="keyspace-set"></a>

### Set Keyspace

```python
session.set_keyspace("udacity")
```

This is very similar to what we used when we connected to Postgres database in the [previous project](https://github.com/sanjeevai/data-modeling-postgres/) and got a cursor to it like this:

```python
conn = psycopg2.connect("host=127.0.0.1 dbname=studentdb user=student password=student")
cur = conn.cursor()
```

<a id="data-modeling"></a>

### Data Modeling

In Apache Cassandra, we model our data **based on the queries we will perform.** Aggregation like GROUP BY, JOIN are highly discouraged in Cassandra. This is because we shouldn't scan the entire data because it is distributed on multiple nodes. It will slow down our system because sending all of that data from multiple nodes to a single machine will crash it.

Now we will create the tables to run the following queries.

1. Give me the artist, song title and song's length in the music app history that was heard during  sessionId = 338, and itemInSession  = 4

2. Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182

3. Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'

To gain more technical detail about the coding portion please view the ETL notebook.

<a id="files"></a>

### Files

<pre>
.
├── Project_1B_\ Project_Template.ipynb--------# Performs ETL
├── event_data---------------------------------# CSV files to be pre-processed
├── event_datafile_new.csv---------------------# Streamlined file to create
                                                 database from
</pre>

<a id="sw_reqs"></a>

### Software Requirements

This project required Python 3.6.x and the necessary libraries are mentioned in _requirements.txt._