---
layout: post
title:  "Data Modeling"
date:   2024-09-17
categories: go_big
---

### What is Data Modeling
数据建模是指创建数据模型的过程。软件工程中，数据工程师在设计数据库时，会将现实世界的各类数据及其关系进行分析、抽象，从中找出内在联系，并将其形式化描述为数据模型，以此建立信息系统的数据库结构。

### Why Data Modeling
1. 帮助理解和组织数据，使其更易于管理和使用；
2. 通过优化数据结构，提高数据存储和检索的效率；
3. 定义数据的约束和规则，确保数据的一致性和完整性；
4. 确保数据模型能够满足业务需求和流程。

### How to Model Data
1. Requirement Analysis
- 定义和分析业务需求；
- 确定数据需求和需要的相应支持。

2. Conceptual Data Model
- 定义实体以及实体之间的关系；
- 使用 ER(Entity-relationship) 图来表示数据的高层次结构，常用 [Lucidchart](https://www.lucidchart.com/pages/?) 来构建概念模型。

3. Logical Data Model
- 在概念模型的基础上添加更多信息，定义表、列、主键和外键；
- 一个概念模型的实现可能需要多个逻辑模型。

4. Physical Data Model
- 根据逻辑模型创建数据库对象，包括数据表的物理位置、物理名字、字段类型以及命名规范；
- 考虑数据库的性能、存储和索引。

### Data Warehouse
数据仓库 (Data Warehouse) 是一个用于存储和分析大量数据的系统，它通常从多个源系统中提取数据，并进行清洗、转换和加载 (ETL) 供数据分析和 BI 报告使用。数据建模在数据仓库的构建过程中同样尤为重要。

#### 数据仓库建模的类型
1. Star Schema
- 星型模型 (Star Schema) 以事实表 (Fact Table) 为中心，周围是维度表 (Dimension Table)；
- 事实表存储度量数据（如: 销售额），维度表存储描述数据（如: 时间、地点、产品）。

2. Snowflake Schema
- 雪花模型 (Snowflake Schema) 是星型模型的扩展，将维度表进一步规范化，分解成多个相关的表；
- 它的特点是减少了数据冗余，但是提高了查询的复杂性。

3. Constellation Schema
- 事实星座模型 (Fact Constellation Schema) 包含多个事实表，适用于复杂的业务场景。

### Data Lake
数据湖 (Data Lake) 和数据仓库一样，是存储大量数据的系统，但数据湖不仅用于存储结构化数据，还用来存储半结构化、非结构化数据。数据湖允许以原始格式存储数据，数据的质量和一致性可能较低。它一般用于存储用户行为数据，如网站日志、社交媒体数据、视频等，以进行大数据分析和机器学习。

### Data Lakehouse
数据湖仓库 (Data Lakehouse) 结合了数据湖和数据仓库的优点，它既能处理结构化数据，也能处理非结构化数据，同时提供了高效的查询性能和数据管理功能。它的代表产品是由 [Databricks](https://docs.databricks.com/en/delta/index.html) 开发的基于 Apache Spark 的开源存储层的 [Delta Lake](https://delta.io/)，以及用于大规模数据集的高性能表格式的 [Apache Iceberg](https://iceberg.apache.org/)。

关于 Delta Lake 可以参见我的这一篇日志：[Delta Lake](_site/go_big/2024/09/19/delta_lake.html).

### 数据建模过程中常见问题及应对措施
>**Problem**: 业务需求不清晰，以及项目进行中需求变更频繁
>
>**Solution**: 
> 1. 深入沟通，明确需求，并编写详细的需求文档；
> 2. 采用迭代开发的方法，分阶段完善数据模型

>**Problem**: 不同数据源数据不一致，数据缺失，数据冗余
>
>**Solution**: 
> 1. 数据清洗，确保数据的一致性和完整性；
> 2. 建立数据验证规则，确保数据的准确性

>**Problem**: 实体之间关系复杂，数据模型复杂
>
>**Solution**: 
> 1. 以目的驱动简化模型，避免不必要的复杂度；
> 2. 将复杂的数据模型分解为多个模块，分别建模和管理

>**Problem**: 存储效率低，查询性能差
>
>**Solution**: 
> 1. 根据实际需求，平衡数据的 Normalization 和 Denormalization；
> 2. 为常用查询字段建立索引；
> 3. 在数据建模过程中进行性能测试
>
>>*注：*
>>
>*1.规范化（Normalization）：规范化是将数据组织成多个相关表的过程，以减少数据冗余和提高数据一致性；*
>>
>>*2.反规范化（Denormalization）：反规范化是将数据合并到较少的表中，以减少查询时的多表连接，从而提高查询性能*

>**Problem**: 敏感数据泄露
>
>**Solution**: 
> 1. 对敏感数据进行加密；
> 2. 严格的访问控制策略，确保只有授权的用户可以访问数据

To be continued...