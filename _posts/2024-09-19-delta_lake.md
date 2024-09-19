---
layout: post
title:  "Delta Lake"
date:   2024-09-19
categories: go_big
---

### What is Delta Lake
[Delta Lake](https://delta.io/) 是一个开源的数据湖存储层 (lakehouse storage layer) 技术，它利用基于文件的事务日志 (file-based transaction log) 扩展了 [Parquet](https://parquet.apache.org/) 数据文件，以实现 ACID 事务和可扩展的元数据处理。Delta Lake 与 Apache Spark API 完全兼容，专为与 Structured Streaming 集成而开发，可以很方便地对 batch & streaming 数据进行处理。

Delta Lake 的主要优势是它对大规模数据处理的可靠性和一致性，同时提供了类似于传统数据库的事务功能。在使用过程中可以通过 Spark SQL 来读取、写入和管理 Delta Lake 中的数据。下面将这些功能一一演示。

### How to Use Delta Lake
#### Install Delta Packege
```bash
pip install delta-spark==2.1.0
```

#### load delta related jars from maven repo
```bash
pyspark --packages io.delta:delta-core_2.12:2.1.0 \
--conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension" \
--conf "spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog" \
--conf "hive.tez.input.format=io.delta.hive.HiveInputFormat"
```

#### Set up a Python Project
```python
from delta import *
from pyspark.sql import SparkSession

builder = SparkSession.builder.appName("DeltaDemo") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")

spark = configure_spark_with_delta_pip(builder).getOrCreate()
```

#### Create a Table
**1. Using Spark SQL DDL**
```python
spark.sql("""
create or replace table {your_database}.delta_demo(
    id int, 
    firstName string, 
    middleName string, 
    lastName string, 
    gender string, 
    birthDate date, 
    ssn string, 
    salary int)
using delta 
location 's3://{S3_BUCKET}/delta_demo';
""")

spark.sql("drop table {your_database}.delta_demo")
```
**2. Using Exsiting Spark Dataframe and change the format to delta**
```python
data = spark.range(0, 5)
data.write.format("delta").save("/tmp/delta-table")
```

#### Append Data
**Write data from existing Spark Dataframe**
```python
from pyspark.sql.types import *
from datetime import date

schema = StructType([
  StructField("id", IntegerType(), True),
  StructField("firstName", StringType(), True),
  StructField("middleName", StringType(), True),
  StructField("lastName", StringType(), True),
  StructField("gender", StringType(), True),
  StructField("birthDate", DateType(), True),
  StructField("ssn", StringType(), True),
  StructField("salary", IntegerType(), True)
])

data = [
  (9999998, 'Billy', 'Tommie', 'Luppitt', 'M', date.fromisoformat('1992-09-17'), '953-38-9452', 55250),
  (9999999, 'Elias', 'Cyril', 'Leadbetter', 'M', date.fromisoformat('1984-05-22'), '906-51-2137', 48500),
  (10000000, 'Joshua', 'Chas', 'Broggio', 'M', date.fromisoformat('1968-07-22'), '988-61-6247', 90000),
  (20000001, 'John', '', 'Doe', 'M', date.fromisoformat('1978-01-14'), '345-67-8901', 55500),
  (20000002, 'Mary', '', 'Smith', 'F', date.fromisoformat('1982-10-29'), '456-78-9012', 98250),
  (20000003, 'Jane', '', 'Doe', 'F', date.fromisoformat('1981-06-25'), '567-89-0123', 89900)
]

data_insert = spark.createDataFrame(data, schema)
data_insert.write.format("delta").mode("append").save("{S3_BUCKET}/delta_demo")
```

#### Read Table
**1. Read Delta table name**
```python
delta_demo_df = spark.table("{your_database}.delta_demo")
delta_demo_df.show(10,0)
```

| id       | firstName | middleName | lastName   | gender | birthDate  | ssn         | salary |
| -------- | --------- | ---------- | ---------- | ------ | ---------- | ----------- |:------ |
| 9999998  | Billy     | Tommie     | Luppitt    | M      | 1992-09-17 | 953-38-9452 | 55250  |
| 9999999  | Elias     | Cyril      | Leadbetter | M      | 1984-05-22 | 906-51-2137 | 48500  |
| 20000002 | Mary      |            | Smith      | F      | 1982-10-29 | 456-78-9012 | 98250  |
| 20000003 | Jane      |            | Doe        | F      | 1981-06-25 | 567-89-0123 | 89900  |
| 10000000 | Joshua    | Chas       | Broggio    | M      | 1968-07-22 | 988-61-6247 | 90000  |
| 20000001 | John      |            | Doe        | M      | 1978-01-14 | 345-67-8901 | 55500  |

**2. Read Delta table by specifying the path to the file**
```python
df = spark.read.format("delta").load("/tmp/delta-table")
df.show()
```

#### Update Table
**1. Using Spark SQL DML**
```python
spark.sql("""
UPDATE {your_database}.delta_demo SET gender = 'Female' WHERE gender = 'F'
""")
spark.sql("""
UPDATE {your_database}.delta_demo SET gender = 'Male' WHERE gender = 'M'
""")
spark.sql("""
CREATE OR REPLACE TEMP VIEW upsert_view (
  id, firstName, middleName, lastName, gender, birthDate, ssn, salary
) AS VALUES
  (9999998, 'Billy', 'Tommie', 'Luppitt', 'M', '1992-09-17', '953-38-9452', 55250),
  (20000003, 'Jane', '', 'Doe', 'F', '1981-06-25', '567-89-0123', 89900)
""")
spark.sql("""
MERGE INTO {your_database}.delta_demo demo
USING upsert_view upsert
ON demo.id = upsert.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *
""")
spark.sql("""
DELETE FROM {your_database}.delta_demo WHERE birthDate < '1980-01-01'
""")
```

| id       | firstName | middleName | lastName   | gender | birthDate  | ssn         | salary |
| -------- | --------- | ---------- | ---------- | ------ | ---------- | ----------- |:------ |
| 9999998  | Billy     | Tommie     | Luppitt    | M      | 1992-09-17 | 953-38-9452 | 55250  |
| 9999999  | Elias     | Cyril      | Leadbetter | Male   | 1984-05-22 | 906-51-2137 | 48500  |
| 20000002 | Mary      |            | Smith      | Female | 1982-10-29 | 456-78-9012 | 98250  |
| 20000003 | Jane      |            | Doe        | F      | 1981-06-25 | 567-89-0123 | 89900  |

> 注：
> Delta Lake 为了解决数据的一致性和可靠性，引入了事务日志和元数据管理来实现 ACID 事务。
> 
> Delta Lake 的物理构成主要是两部分：
> 1. 以 Parquet 格式存储在文件系统的数据文件，任何数据操作，数据文件只增不减；
> 2. 在数据文件的同级目录下，有一个文件夹专门存放 Json 格式的事务日志，它用来记录所有对数据的操作，每一次操作都会生成一个日志文件，记录这次操作的详细信息。
>
> 基于以上架构，在每次读取 Delta Lake 数据时，会自动根据事务日志生成一个数据快照，并将该快照的结果返回成 Dataframe。

**2. Using Delta Lake API**
```python
from delta.tables import *
from pyspark.sql.functions import *

deltaTable = DeltaTable.forPath(spark, "/tmp/delta-table")

# Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })

# Delete every even value
deltaTable.delete(condition = expr("id % 2 == 0"))

# Upsert (merge) new data
newData = spark.range(0, 20)

deltaTable.alias("oldData") \
  .merge(
    newData.alias("newData"),
    "oldData.id = newData.id") \
  .whenMatchedUpdate(set = { "id": col("newData.id") }) \
  .whenNotMatchedInsert(values = { "id": col("newData.id") }) \
  .execute()

deltaTable.toDF().show()
```
#### Read older versions of data
**Delta Lake 提供方式查询每次数据更改后的数据版本的快照**
```python
df = spark.read.format("delta") \
  .option("versionAsOf", 0) \
  .load("/tmp/delta-table")

df.show()
```
#### Write a stream of data to a table
**支持将 Structured Streaming 的流式数据集写入 Delta table**
```python
streamingDf = spark.readStream.format("rate").load()

stream = streamingDf \
  .selectExpr("value as id") \
  .writeStream.format("delta") \
  .option("checkpointLocation", "/tmp/checkpoint") \
  .start("/tmp/delta-table")
```

#### Read Delta table as a streaming source
**将 Delta 表的更新读进流式数据中**
```python
stream2 = spark.readStream.format("delta") \
  .load("/tmp/delta-table") \
  .writeStream.format("console") \
  .start()
```

### Delta Lake on Unstructured and Semi-structured Data
依照同样的原理，Delta Lake 也可以用来处理 Json、XML、Avro 等半结构化数据，以及文本、图像、音频、视频等非结构化的数据。

**1. 以 Json 数据为例，将半结构化对象存入 Delta Table**
```python
from pyspark.sql import SparkSession

# 创建 Spark Session
spark = SparkSession.builder \
    .appName("DeltaLakeExample") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
    .getOrCreate()

# 读取 JSON 数据
json_df = spark.read.json("/path/to/json/files")

# 将 JSON 数据写入 Delta Lake 表
json_df.write.format("delta").mode("append").save("/tmp/delta-table-json")

# 读取 Delta Lake 表中的 JSON 数据
delta_json_df = spark.read.format("delta").load("/tmp/delta-table-json")
delta_json_df.show()
```

**2. 以文本数据为例，将非结构化对象存入 Delta Table**
```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *

# 创建 Spark Session
spark = SparkSession.builder \
    .appName("DeltaLakeExample") \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
    .getOrCreate()

# 定义 schema
schema = StructType([
    StructField("id", IntegerType(), True),
    StructField("text", StringType(), True)
])

# 创建数据
data = [
    (1, "This is a sample text."),
    (2, "Another example of text data.")
]

# 创建 DataFrame
text_df = spark.createDataFrame(data, schema)

# 将文本数据写入 Delta Lake 表
text_df.write.format("delta").mode("append").save("/tmp/delta-table-text")

# 读取 Delta Lake 表中的文本数据
delta_text_df = spark.read.format("delta").load("/tmp/delta-table-text")
delta_text_df.show()
```

To be continued...