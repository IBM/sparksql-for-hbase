# Spark SQL access to NOSQL HBase Tables

In this developer journey, we show you an example using Spark and HSpark connector package to query against data tables reside in HBase region servers. 

When the readers has completed this journey, they will understand how to:

 - Install and configure [Apache Spark](https://spark.apache.org/) and [HSpark](https://github.com/bomeng/HSpark) connector
 - Learn to create metadata for tables in [Apache HBase](https://hbase.apache.org/)
 - Write Spark SQL queries to retrieve HBase data for analysis

### Overview

This journey aims to help application developers who are familiar with SQL while want to access HBase data tables as well. We will show you how to quickly create and query the data table by using Apache Spark and HSpark connector package, thus eliminating the need to learn Java API to access HBase data table. You can also take the advantage of the performance benefit by using HBase.

From this journey, you will learn how to configure the HSpark and Spark to set up the environment; create the data tables using HSpark; insert data into the tables; and finally query the data tables using HSpark.
With Apache Spark and HSpark package, you will be able to easily access HBase data table while keeping the performance at its best.

Included Components:
 - [Apache Spark 2.2.0](https://spark.apache.org/)
 - [Apache HBase 1.2.4](https://hbase.apache.org/)
 - [HSpark 2.2.0](https://github.com/bomeng/HSpark)

### Steps to install and configure HSpark

1. Install Apache Spark 2.2.0

HSpark relies on Apache Spark, thus you need to install Apache Spark first. Download Apache Spark 2.2.0 from the following link:

```sh
https://spark.apache.org/downloads.html
```

Apache Spark has an online document to guide user how to set up and configure Apache Spark:

```sh
https://spark.apache.org/docs/latest/
```

In order to make HSpark work properly, you may need to set SPARK_HOME environment to point to your installation directory.

2.	Install Apace HBase 1.2.4

Currently, HSpark works with Apache HBase 1.2.4. Download this version from the following link:

```sh
https://archive.apache.org/dist/hbase/1.2.4/
```

HBase can be installed in 3 running modes – standalone, pseudo-distributed, and fully distributed mode. Follow this link to install HBase in your preferred mode to test:

```sh
https://hbase.apache.org/book.html#quickstart
```

But for simple demonstration of HSpark in a single machine, you can run it using pseudo-distributed mode. In this mode, HBase still runs completely on a single host, but each daemon runs as a separate process. 

3.	Download and build HSpark

First you need to use git to clone the source codes from github and set up an environment property $HSPARK_HOME:

```sh
git clone https://github.com/bomeng/HSpark.git
export HSPARK_HOME=<path_to_hspark>
```

Configure the proper values of hspark.properties in the HSpark/conf folder. Depends on your HBase running mode, you can leave the value as it is, or modify them similar to the comments as an example.

Then go to the root of the source tree and use Apache Maven to build the project:

```sh
cd HSpark
mvn -DskipTests clean install
```

In order to use HSpark SQL shell, you will need to add the built jar to the HBASE classpath:

```sh
export HBASE_CLASSPATH=<path_to_hspark>/hspark-2.2.0.jar
```

4. Start the HSpark shell

HSpark shell is a convenient tool for the developers or users to try HSpark quickly. It supports the basic SQL commands to create table, import data and query the table. After we have installed Spark, HBase and HSpark, now we start the HSpark shell:

```sh
cd <path_to_hspark>
./bin/hbase-sql
```
### Using HSpark to access TPC-DS data

[The TPC Benchmark™DS (TPC-DS)](http://www.tpc.org/tpcds/) is a decision support benchmark that models several generally applicable aspects of a decision support system, including queries and data maintenance. TPC-DS is the de-facto industry standard benchmark for measuring the performance of decision support solutions including, but not limited to, Big Data systems.

In order to demonstrate HSpark’s capability, we will use some of the TPC-DS schema to create the tables in the HBase and then import the sample data into those tables. After that, we can use HSpark to do various queries against the tables we’ve created.

1.	Schemas

We will take some tables from TPC-DS definition. Here is the schema, the primary keys in each table are marked by underline:

[![N|Solid](https://github.com/bomeng/hspark_journey/blob/master/resources/schema.png)](https://github.com/bomeng/hspark_journey/blob/master/resources/schema.png)

2. Create the tables using script in the HSpark shell

Currently, HSpark support several data types that are commonly used. For TPC-DS schema, the data type can be mapped as followings:

| TPC-DS data type | HSpark data type |
| ------ | ------ |
| Identifier | Integer |
| Integer | Integer |
| Decimal(d, f) | Float or Double |
| Char(N) or Varchar(N) | String |
| Date | Long |

Please find the table creation commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder.

3.	Import data into the tables using script in the HSpark shell

HSpark supports bulk-load data into the tables. The data can be defined in the CSV files. By using TPC-DS tool, you can generate the data at your preferred size.

HSpark can import CSV data file that you generate by using TPC-DS tool. Import the data into different tables using the script:

```sh
LOAD DATA LOCAL INPATH ‘<path_to_csv_file>’ INTO TABLE <table_name>
```

Please find the data import commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder. Sample data files can be found in the [data](https://github.com/bomeng/hspark_journey/tree/master/data) folder.

4. Query the tables using script in the HSpark shell

With some data imported into the tables and we can then query the tables using the regular SQL queries, for example,

```sh
SELECT count(1) FROM store_sales
```

More query examples can be found in [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder.

5. Using HSpark programmatically

HSpark can be used programmatically as well to create the tables, import data or query the tables. More examples can be found in the [test](https://github.com/bomeng/HSpark/tree/master/src/test) folder of HSpark source codes.
