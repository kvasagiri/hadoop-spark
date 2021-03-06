# Hadoop Setup

## Working Directory
hadoop-setup

## STEP 1 : Login to Sandbox
Follow instructions for your system.

## STEP 2 : Inspect Clickstream data

Clickstream file in :   data/clickstream/clickstream.csv

```
1420070400000,ip_1,user_5,clicked,facebook.com,campaign_6,139,session_98
1420070400864,ip_2,user_3,viewed,facebook.com,campaign_4,35,session_98
1420070401728,ip_8,user_8,clicked,youtube.com,campaign_12,115,session_92

```

## STEP 3 : Go to working directory
```
    $   cd  ~/hadoop-spark/hadoop-setup
```

## STEP 4 : Copy Clickstream data into HDFS

```
    $   hdfs dfs  -mkdir  -p   clickstream/in

    $   hdfs  dfs  -put   ../data/clickstream/clickstream.csv    clickstream/in/
```

Generate more clickstream data using the given python script, and copy these into HDFS as well.

```
    $   python ../data/clickstream/gen-clickstream.py

    $   hdfs  dfs  -put   *.csv    clickstream/in/
```

And inspect the files in HDFS using CLI and HDFS UI.

```
    $   hdfs  dfs  -ls  clickstream/in/
```

* Launch HDFS UI from 'Ambari --> HDFS --> Quick Links --> Namenode UI'.
* Then launch the file browser under 'Utilities -> File Browser'.
* Navigate to HDFS directory :  /user/root/clickstream/in


## STEP 5 : Setup HIVE Table for Clickstream

Inspect the `clickstream.hql` file in this directory.

```
    CREATE EXTERNAL TABLE clickstream (
        ts BIGINT,
        ip STRING,
        userid STRING,
        action STRING,
        domain STRING,
        campaign STRING,
        cost INT,
        session STRING)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ','
    stored as textfile
    LOCATION '/user/root/clickstream/in'  ;
```

Execute this file as follows to create a Hive table:

```
    $    hive   -f    clickstream.hql
```


## STEP 6 : Hive Queries

Now let's do some Hive queries on our data

Launch the Hive shell as follows
```
    $    hive
```

And execute the queries in the shell:

```sql

-- see the tables
    show tables;

-- inspect clickstream table
    desc clickstream;

-- quick view of data
    select * from clickstream limit 10;

-- aggregate query on actions
    select action, count(*) as total from clickstream group by action;

-- find max / min of costs
    select MAX(cost), MIN(cost) from clickstream;

```

