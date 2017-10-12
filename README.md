# Spark SQL access to NOSQL HBase Tables

Apache HBase is an open source, NOSQL and distributed database which runs on top of HDFS and is well-suited for faster read and write operations on large datasets with high throughput and low input/output latency. Unlike relational and traditional databases, HBase lacks of support of SQL scripting, data types, etc. it will use Java API to achieve the equivalent. 

This journey aims to help application developers who are familiar with SQL while want to access HBase data tables as well. We will show you how to quickly create and query the data table by using Apache Spark and HSpark connector package, thus eliminating the need to learn Java API to access HBase data table. You can also take the advantage of the performance benefit by using HBase.

When the readers have completed this journey, they will understand how to:

 - Install and configure [Apache Spark](https://spark.apache.org/) and [HSpark](https://github.com/bomeng/HSpark) connector
 - Learn to create metadata for tables in [Apache HBase](https://hbase.apache.org/)
 - Write Spark SQL queries to retrieve HBase data for analysis

![Architecture diagram](resources/flow.png)

## Flow

1. Set up the environment (Apache Spark, Apache HBase, and HSpark).
2. Create the tables using HSpark.
3. Load the data into the tables.
4. Query the data using HSpark Shell.

## Included Components:
 - [Apache Spark 2.2.0](https://spark.apache.org/)
 - [Apache HBase 1.2.4](https://hbase.apache.org/)
 - [HSpark 2.2.0](https://github.com/bomeng/HSpark)

Other tools you may need to complete this journey (please refer to corresponding documents to install those components, you may also need to properly set up system environment such as `PATH`, `JAVA_HOME` and `MVN_HOME`):
 - [Apache Maven 3.5.0](https://maven.apache.org/)
 - [Java JDK 1.8.0_144](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

## Steps

Follow these steps to create required components and open the hbase shell locally.

1. [Install Apache Spark](#1-install-apache-spark)
2. [Install Apace HBase](#2-install-apache-hBase)
3. [Download and build HSpark](#3-download-and-build-hspark)
4. [Start the HSpark shell](#4-start-the-hspark-shell)

### 1. Install Apache Spark

HSpark relies on Apache Spark, thus you need to install Apache Spark first. Download Apache Spark 2.2.0 from the following link:

```sh
https://spark.apache.org/downloads.html
```

You may also download the source codes from [Apache Spark GiHub](https://github.com/apache/spark) and use 2.2 branch to build it.

Apache Spark has an online document to guide user how to set up and configure Apache Spark:

```sh
https://spark.apache.org/docs/latest/
```

In order to make HSpark work properly, you may need to set `SPARK_HOME` environment to point to your installation directory.

### 2.	Install Apache HBase

Currently, HSpark works with Apache HBase 1.2.4. Download this version from the following link:

```sh
https://archive.apache.org/dist/hbase/1.2.4/
```

HBase can be installed in 3 running modes – standalone, pseudo-distributed, and fully distributed mode. Follow this link to install HBase in your preferred mode to test:

```sh
https://hbase.apache.org/book.html#quickstart
```

But for simple demonstration of HSpark in a single machine, you can run it using pseudo-distributed mode. In this mode, HBase still runs completely on a single host, but each daemon runs as a separate process.

Please also properly set up the environment variablle of `HBASE_HOME` and its `PATH`.

### 3. Download and build HSpark

First you need to use git to clone the source codes from github and set up an environment property `HSPARK_HOME`:

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

### 4. Start the HSpark shell

HSpark shell is a convenient tool for the developers or users to try HSpark quickly. It supports the basic SQL commands to create table, import data and query the table. After we have installed Spark, HBase and HSpark, now we start the HSpark shell:

```sh
cd <path_to_hspark>
./bin/hbase-sql
```
## Using HSpark to access TPC-DS data

[The TPC Benchmark™DS (TPC-DS)](http://www.tpc.org/tpcds/) is a decision support benchmark that models several generally applicable aspects of a decision support system, including queries and data maintenance. TPC-DS is the de-facto industry standard benchmark for measuring the performance of decision support solutions including, but not limited to, Big Data systems.

In order to demonstrate HSpark’s capability, we will use some of the TPC-DS schema to create the tables in the HBase and then import the sample data into those tables. After that, we can use HSpark to do various queries against the tables we’ve created.

### 1. Schemas

We will take some tables from TPC-DS definition. Here is the schema, the primary keys in each table are marked by underline:

[![N|Solid](resources/schema.png)](resources/schema.png)

### 2. Create the tables using script in the HSpark shell

Currently, HSpark support several data types that are commonly used. For TPC-DS schema, the data type can be mapped as followings:

| TPC-DS data type | HSpark data type |
| ------ | ------ |
| Identifier | Integer |
| Integer | Integer |
| Decimal(d, f) | Float or Double |
| Char(N) or Varchar(N) | String |
| Date | Long |

Please find the table creation commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder.

### 3. Import data into the tables using script in the HSpark shell

HSpark supports bulk-load data into the tables. The data can be defined in the CSV files. By using TPC-DS tool, you can generate the data at your preferred size.

HSpark can import CSV data file that you generate by using TPC-DS tool. Import the data into different tables using the script:

```sh
LOAD DATA LOCAL INPATH ‘<path_to_csv_file>’ INTO TABLE <table_name>
```

Please find the data import commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder. Sample data files can be found in the [data](https://github.com/bomeng/hspark_journey/tree/master/data) folder.

### 4. Query the tables using script in the HSpark shell

With some data imported into the tables and we can then query the tables using the regular SQL queries, for example,

```sh
SELECT count(1) FROM store_sales
```

More query examples can be found in [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder, you can also find the output of the examples in the scripts.

### 5. Using HSpark programmatically

HSpark can be used programmatically as well to create the tables, import data or query the tables. More examples can be found in the [test](https://github.com/bomeng/HSpark/tree/master/src/test) folder of HSpark source codes.

## Troubleshooting

* Error: `java.io.IOException: Mkdirs failed to create /user/<userid>/hbase-staging`

If you see this error while running the LOAD command, it means you need to create a temparory folder locally to allow hbase to store intermediate result.

> Solution: Create the *hbase-staging* directory that is required by hbase (using `mkdir` and `chmod` to create the directory manually).

## License
[Apache 2.0](LICENSE)
