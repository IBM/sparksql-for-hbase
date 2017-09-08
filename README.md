# Spark SQL access to NOSQL HBase Tables

In this developer journey, we show you an example using Spark and HSpark connector package to query against data tables reside in HBase region servers. When you complete this journey, you will understand how to:
 - Install and configure Spark and HSpark connector
 - Learn to create Metadata for Big Tables in HBase
 - Write Spark SQL queries to retrieve HBase data for analysis

### Overview

This journey aims to help application developers who are familiar with SQL while want to access HBase data tables as well. We will show you how to quickly create and query the data table by using Apache Spark and HSpark connector package, thus eliminating the need to learn Java API to access HBase data table. You can also take the advantage of the performance benefit by using HBase.

From this journey, you will learn how to configure the HSpark and Spark to set up the environment; create the data tables using HSpark; insert data into the tables; and finally query the data tables using HSpark.
With Apache Spark and HSpark package, you will be able to easily access HBase data table while keeping the performance at its best.

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
