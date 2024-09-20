---
layout: post
title:  "Hands on Delta Lake"
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
**Delta Lake 提供方式查询历次数据更改后的数据版本的快照**
```python
df1 = spark.read.format("delta") \
  .option("versionAsOf", 0) \
  .load("/tmp/delta-table")

df2 = spark.read.format("delta") \
  .option("timestampAsOf", timestamp_string) \
  .load("/tmp/delta-table")
```

**Data Retention**

为了访问先前版本的 Delta table 数据，必须完整地保留数据文件和事务日志。默认情况下，数据文件不会自动被清除，表的历史快照会被保留 30 天。
1. 清除数据文件，执行 [VACUUM](https://docs.delta.io/2.1.0/delta-utility.html#-delta-vacuum)

```sql
-- vacuum files not required by versions older than the default retention period
VACUUM {your_database}.delta_demo

-- vacuum files in path-based table
VACUUM '/data/events'
VACUUM delta.`/data/events/`

-- vacuum files not required by versions more than 100 hours old
VACUUM delta.`/data/events/` RETAIN 100 HOURS

-- do dry run to get the list of files to be deleted
VACUUM {your_database}.delta_demo DRY RUN
```

2. 改变数据留存时间，改变 [Table properties](https://docs.delta.io/2.1.0/delta-batch.html#-table-properties)

```sql
--controls how long the history for a table is kept. The default is interval 30 days.
ALTER TABLE {your_database}.delta_demo SET TBLPROPERTIES ('delta.logRetentionDuration' = 'interval <interval>');
```

```sql
--To access historical data even if you run VACUUM on the Delta table, This setting may cause your storage costs to go up.
ALTER TABLE {your_database}.delta_demo SET TBLPROPERTIES ('delta.deletedFileRetentionDuration' = 'interval <interval>');
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

### Hive Insert Overwrite VS Delta Lake Update
#### Hive Insert Overwrite
**Advantages:**
1. **Simplicity**: Hive 的 overwrite 功能简单易用，特别是在需要完全替换表中数据时；
2. **Compatibility**: Hive 与 Hadoop 生态系统紧密集成，与各种基于 Hadoop 的工具和框架兼容；
3. **Flexibility**: 可以用于 overwrite 整个表或特定分区，提供数据管理的灵活性。

**Disadvantages:**
1. **Limited Update Support**: 缺乏细粒度更新的能力（如更新特定的行或列），对于细粒度更新的情景效率低下；
2. **Resource-Intensive**: overwrite 大表涉及删除现有数据并写入新数据，消耗大量资源，影响性能和存储利用率；
3. **No Transaction Support**: Hive 的 overwrite 操作不支持 ACID 事务，不利于在并发环境中保持数据完整性。

#### Delta Lake Update
**Advantages:**
1. **ACID Transactions**: Delta Lake 提供完整的 ACID 事务，确保在并发读写操作中数据的完整性和一致性；
2. **Fine-Grained Updates**: 支持细粒度更新，允许更高效和有针对性的数据操作；
3. **Schema Evolution**: Delta Lake 支持模式演化，允许对表 Schema 进行更改而无需重写整个数据集；
4. **Optimized Performance**: Delta Lake 的优化存储格式和事务功能有助于提高大规模数据集处理性能。

**Disadvantages:**
1. **Dependency on Spark**: Delta Lake 与 Apache Spark 紧密集成，受限于不能直接与其他数据处理框架一起使用；
2. **Limited Compatibility**: Delta Lake 与 Hive 的集成存在限制，主要在元数据管理方面；
3. **Complexity**: ACID 事务和模式演化功能增加了复杂性，需要更深入地理解底层存储和处理机制。

#### Summary
总之，Hive 的 overwrite 功能提供了简单性和兼容性，但缺乏细粒度更新支持和 ACID 事务；Delta Lake提供 ACID 事务、精细更新、模式演化和优化性能，但依赖于 Spark，与其他框架的兼容性有限。

具体的选择取决于用例的要求，包括对事务支持、精细更新和现有技术栈的需求。

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