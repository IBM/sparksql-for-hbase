# Using Spark SQL to access NOSQL HBase Tables

Apache HBase is an open source, NOSQL distributed database which runs on top of the Hadoop Distributed File System (HDFS), and is well-suited for faster read/write operations on large datasets with high throughput and low input/output latency. But, unlike relational and traditional databases, HBase lacks support for SQL scripting, data types, etc., and requires the Java API to achieve the equivalent functionality. 

This journey is intended to provide application developers familiar with SQL, the ability to access HBase data tables using the same SQL commands. You will quickly learn how to create and query the data tables by using Apache Spark SQL and the HSpark connector package. This allows you to take advantage of the significant performance gains from using HBase without having to learn the Java APIs required to traditionally access the HBase data tables.

When you have completed this journey, you will understand how to:

 - Install and configure [Apache Spark](https://spark.apache.org/) and [HSpark](https://github.com/bomeng/HSpark) connector.
 - Learn to create metadata for tables in [Apache HBase](https://hbase.apache.org/).
 - Write Spark SQL queries to retrieve HBase data for analysis.

![Architecture diagram](resources/flow.png)

## Flow

1. Set up the environment (Apache Spark, Apache HBase, and HSpark).
2. Create the tables using HSpark.
3. Load the data into the tables.
4. Query the data using HSpark Shell.

## Included Components:
 - [Apache Spark](https://spark.apache.org/): An open-source, fast and general-purpose cluster computing system.
 - [Apache HBase](https://hbase.apache.org/): A distribute key/value data store built to run on top of HDFS.
 - [HSpark](https://github.com/bomeng/HSpark): Provides access to HBase using SparkSQL. 

There are also some core tools you will need to complete this journey. If you do not already have them installed, please refer to the corresponding documents for installation instructions. Ensure that the proper system environemnt variables are correctly set (such as `PATH`, `JAVA_HOME` and `MVN_HOME`).

 - [Apache Maven 3.5.0](https://maven.apache.org/): A build automation tool primarily used for Java projects.
 - [Java JDK 1.8.0_144](http://www.oracle.com/technetwork/java/javase/downloads/index.html): A software development environment used for developing Java applications.

# Steps

Perform the following steps:

1. [Install Apache Spark](#1-install-apache-spark)
2. [Install Apace HBase](#2-install-apache-hbase)
3. [Download and build HSpark](#3-download-and-build-hspark)
4. [Start the HSpark shell](#4-start-the-hspark-shell)
5. [Use HSpark to access TPC-DS data](#5-use-hspark-to-acces-tpc-ds-data)

## 1. Install Apache Spark

HSpark relies on Apache Spark, thus you need to install Apache Spark first. Download Apache Spark 2.2.0 from the [downloads page](https://spark.apache.org/downloads.html). 

You may also download the source codes from [Apache Spark GiHub](https://github.com/apache/spark) and build it using the 2.2 branch.

To set-up and configure Apache Spark, please refer to the [on-line users guide](https://spark.apache.org/docs/latest/).

In order to make HSpark work properly, you may need to set the `SPARK_HOME` environment variable to point to your installation directory:

```sh
$ export SPARK_HOME=<path_to_spark>
```

## 2. Install Apache HBase

Currently, HSpark works with Apache HBase 1.2.4. Go to the [downloads page](https://archive.apache.org/dist/hbase/1.2.4/) to install it. 

HBase can be installed to run in 3 different modes – standalone, pseudo-distributed, and fully distributed. 

For our simple demonstration of running HSpark in a single machine, we recommend you run it in pseudo-distributed mode. This mode runs HBase completely on a single host, but all daemons run as a separate process.

For reference, refer to this [guide](https://hbase.apache.org/book.html#quickstart) to review how to install and run in each of the modes.

Also properly set up the environment variable `HBASE_HOME` and add it to `PATH`.

## 3. Download and build HSpark

Use git to clone `version 2.2.0` of the source code from github and set up an environment property `HSPARK_HOME`:

```sh
$ git clone https://github.com/bomeng/HSpark.git
$ export HSPARK_HOME=<path_to_hspark>
```

Configure the proper values in `hspark.properties` found in the `HSpark/conf` folder. Depending on your HBase running mode, you can leave the values as is, or modify them as specified in the comments found in the file.

Go to the root of the source tree and use Apache Maven to build the project:

```sh
$ cd HSpark
$ mvn -DskipTests clean install
```

In order to use the HSpark SQL shell, you will need to add the built HSpark jar to the HBASE classpath:

```sh
$ export HBASE_CLASSPATH=$HSPARK_HOME/target/hspark-2.2.0.jar
```

## 4. Start the HSpark shell

HSpark shell is a convenient tool for the developers or users to try HSpark quickly. It supports the basic SQL commands to create tables, import data and perform queries. After we have installed Spark, HBase and HSpark, now we can start the HSpark shell:

```sh
$ cd <path_to_hspark>
$ ./bin/hbase-sql
```

## 5. Use HSpark to access TPC-DS data

[The TPC Benchmark™DS (TPC-DS)](http://www.tpc.org/tpcds/) is a decision support benchmark that models several generally applicable aspects of a decision support system, including queries and data maintenance. TPC-DS is the de-facto industry standard benchmark for measuring the performance of decision support solutions including, but not limited to, Big Data systems.

In order to demonstrate HSpark’s capability, we will use some of the TPC-DS schema to create the tables in HBase and then import the sample data into those tables. We can then use HSpark to do various queries against these tables.

### 1. Schemas

We will take some tables from the TPC-DS definition. Here is the schema (the primary keys in each table are underlined):

[![N|Solid](resources/schema.png)](resources/schema.png)

### 2. Create the tables using script in the HSpark shell

Currently, HSpark supports several data types that are commonly used. For the TPC-DS schema, the data types can be mapped as the following:

| TPC-DS data type | HSpark data type |
| ------ | ------ |
| Identifier | Integer |
| Integer | Integer |
| Decimal(d, f) | Float or Double |
| Char(N) or Varchar(N) | String |
| Date | Long |

Please find the table creation commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder. There you will find 5 commands that you need to cut and paste into the HSpark Shell.

### 3. Import data into the tables using script in the HSpark shell

HSpark supports bulk-load of data into the tables. The data can be defined in CSV files. By using the TPC-DS tool, you can generate the data at your preferred size.

HSpark can import CSV data file that you generate by using the TPC-DS tool. Import the data into different tables using the script:

```sh
LOAD DATA LOCAL INPATH ‘<path_to_csv_file>’ INTO TABLE <table_name>
```

Please find the data import commands in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder. Sample data files can be found in the [data](https://github.com/bomeng/hspark_journey/tree/master/data) folder.

### 4. Query the tables using script in the HSpark shell

After importing the data into the tables, we can now query the tables using regular SQL queries; for example,

```sh
SELECT count(1) FROM store_sales
```

More query examples can be found in the [scripts](https://github.com/bomeng/hspark_journey/tree/master/scripts) folder, along with expected ouput from each of the queries.

### 5. Using HSpark programmatically

HSpark can also be used to programmatically create tables, import data and run queries. Examples can be found in the [test](https://github.com/bomeng/HSpark/tree/master/src/test) folder of HSpark source code repo.

# Troubleshooting

* Error: `java.io.IOException: Mkdirs failed to create /user/<userid>/hbase-staging`

If you see this error while running the LOAD command, it means you need to create a temparory folder locally to allow hbase to store intermediate result.

> Solution: Create the *hbase-staging* directory that is required by hbase (using `mkdir` and `chmod` to create the directory manually).

* Error: `java.net.ConnectException: Connection refused`

If you see this error while running the command, it usually means HSpark cannot connect to HBase, either it is due to connection configuration issue, or HBase is not up and running.

> Solution: Make sure HBase is properly configured and is running. You can also use `jps` to check if it is running.

* Error: `org.apache.spark.sql.catalyst.parser.ParseException`

If you see this error while running any command, it means your command syntax is not correct.

> Solution: Make sure your command is just one single line and has the correct syntax, especially for the single quote, space, etc. Although some example scripts look like multi-line commands, they are actually just single-line commands.

# License
[Apache 2.0](LICENSE)
