# Performance comparison of different file formats (Queries)


Data Lakes let you choose the format that best fit your data and your use cases. This is a lot more freedom compared to the Data Warehouse paradigm which impose a rigid, closed and proprietary format. Data Warehouses were purpose-build for BI and reporting but they had no support for video, audio and text which made up the majority of large data sources. Also, they have limited support for realtime streaming which is becoming increasingly important. Today, most of the data is first stored in a Data Lake or a blob storage and only a subset is moved into the Data Warehouse. With [Delta Lake](/en/tag/delta-lake/) and the latest version if [Hive](/en/tag/apache-hive/), you can enhance your Data Lake into what Databricks call a Data Lakehouse, providing transactionnal queries and streaming support to you SQL layer while preserving direct access to raw data files for Data Science and Machine Learning.

The choice of format to store your data is crucial and no format is born equal. Some are optimized for analytics, others for batch and streaming. Some are human readable, other and machine interpreted. In our previous article, we compared [different file formats used in Big Data](/en/2020/07/23/benchmark-study-of-different-file-format/). The same file formats storing the same datasets will be re-used. Different queries were crafted to illustrate different types of stress and usecases. Data are exposed inside Hive and the processing engine is Tez for all the queries.

## Set up environment 

Hive includes statistical support in its metastore. It generally serves as an input of the cost function in order to optimize requests. This is an advantage when executing complex requests. Our objective is to measure raw performances of multiple file formats. Thus, any tuning and optimization hint provided by Hive is disabled. 

The `hive.compute.query.using.stats` property takes into account the statistic like min, max and count(1) from the metastore during the execution query.

```sql
Set hive.compute.query.using.stats=false;
```

The `hive.stats.autogather` property disables the gathering and updating statistics automatically during Hive DML (Data Manipulation Language) operations.

```sql
set hive.stats.autogather=false;
```

The `hive.stats.fetch.column.stats` property disables the column statistics from metastore

```sql
Set hive.stats.fetch.column.stats=false;
```

Additionally, there are lot of optimizations properties that can be applied in Hive. Among that, we can notice the use of Tez as execution engine, compression, [join optimization](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization), [cost-based optimization](https://cwiki.apache.org/confluence/display/Hive/Cost-based+optimization+in+Hive), [vectorization](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution) and so one. You will get more information [there](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=40507602#ConfigurationProperties) about these optimized configurations. These following optimization are turned off.

By default, Hive filters and stores common queries in a cache. It retrieves the query result from a cache instead of recomputing the result.  

```sql
Set hive.query.results.cache.enabled=false;
```

CBO optimizes and calculates the cost of various plans for a query. It reduces the execution time and resource utilizations. 

```sql
--Disable CBO
Set hive.cbo.enable=false;
```

We turned off the conversion automatic of common join into mapjoin based on the input file size.

```sql
Set hive.auto.convert.join=false;
```

Using `hive.auto.convert.join.noconditionaltask`, you can combine three or more map-side joins into a single map-side join if size of n-1 table is less than 10 MB.

```sql
Set hive.auto.convert.join.noconditionaltask = false;
```

Disable the [bucket](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL+BucketedTables) join.

```sql
SET hive.optimize.bucketmapjoin = false;
```

Deactivate the bucketed group by from bucketed partitions/tables.

```sql
Set hive.optimize.groupby = false;
```

The following setting doesn't inform Hive to enable skew join optimization.  

```sql
set hive.optimize.skewjoin = false;
```

Vectorization enables Hive to process a batch of lines simultaneously instead of one line at a time. 

```sql
Set hive.vectorized.execution.enabled = false;
```

## Performance evaluation criteria 

Some metrics are provided by Hive and YARN while some others are specific to the selected engine such as [MR](/en/tag/MapReduce) and [Tez](https://tez.apache.org/). In our case, the Tez engine is used by default otherwise it can be set up as below. The metrics recorded from Tez are the `CPU` and `duration` time. 

```sql
SET hive.execution.engine = tez
```

## Reading requests

Some queries are tested such as, aggregation, join, data lookup and eventually additional features. Each request type is tested in decompression and compression case. With some request type, imdb data is not used due its small size of data which doesn't capture well the performance. It is only tested in join request since its structure is adapted on that. Between the different tables it contains, we took those with a large size which is imdb_title_principal and imdb_title_akas. 

### Aggregation requests

Data aggregation is the process of gathering data and presenting it in a summarized format. Hive offers several built-in aggregate functions, such as count, max(...), group by(...), and avg(...),... Let's take a look at some of them.

- **Browsing the entire dataset**

The Count (star) without condition is the request type appropriate for browsing your table. It counts the total number of records.

```sql
--Trip data
SELECT COUNT(*) 
FROM trip_data;
```

![Count_start_datasets](feature/plot/count(*)trip.png)

In trip data, ORC and Parquet are respectively the appropriate format for browsing faster a table. Between row storage format, CSV takes up less time both in CPU and duration that other row formats. JSON doesn't perform this task because it takes lot of CPU resources. However, without compression, CSV provides less processing time than Parquet what is very surprising as result. This can be explained to the fact contrary to CSV, Parquet requires to transform data into readable format as its data is binary before to read the entire record. This could imply additional time to Parquet for achieving the process.

```sql
--Wikipedia data
SELECT COUNT(*)
FROM Wikipedia ;
```

![Count_start_datasets](feature/plot/count(*)wiki.png)

In Wikipedia, ORC and Parquet are also very efficient for reading the entire table. In decompression case, AVRO takes up less duration time but it provides important CPU resources than JSON. This could be explained by the same reason described previously between CSV and Parquet in trip data. Unlike compression, AVRO optimizes better while JSON generates more overhead of time. This significant amount of time from JSON could be due to compression type used for it (bzip2). Because bzip2 stands on a slower compression/decompression process even if it performs well the compression ratio. 

- **Fetch the max values**

The MAX function is aggregation function allowing to get the maximum values from numeric column. 

```sql
-- Trip data
SELECT MAX (trip_distance)
FROM trip_data;
```

![Max data](feature/plot/max_trip.png)

ORC followed by Parquet obtains quickly the maximum value within a numeric column. CSV has an execution time not too far of ORC and Parquet without compression. Plus, it takes less time than row formats mainly in CPU time. It optimizes well in compression but its difference with AVRO is not very significant. These results show that promoting column formats will be an efficient choice within a columns and eventually CSV from row formats.

```sql
-- Wikipedia data
SELECT MAX (claims['p1245'].mainsnak.datavalue.`value`)
FROM Wikipedia;
```

![Max wikipedia](feature/plot/max_wiki.png)

With Wikipedia, the same observation is noted as in count(`*`) request. 

- **Grouping data**

The GROUP BY statement is used to organize identical data into groups with the help of some functions like AVG(), Count(),... 

```sql
-- Trip data
SELECT AVG(trip_distance), vendor_id
FROM trip_data
GROUP BY vendor_id;
```

![Group by](feature/plot/group_trip.png)

As usual, ORC and Parquet consume less resources for grouping data. Between row formats, grouping data with CSV performs more than JSON and AVRO. JSON does not optimize this request mainly when the data is compressed. 

```sql
-- Wikipedia data
SELECT COUNT (id), type
FROM Wikipedia
GROUP BY type;
```

![Group by](feature/plot/group_wiki.png)

The result with Wikipedia provides the same observation as the previous queries. 

### Joint request

JOIN request creates a link between two or more tables in order to retrieve a specific result. Imbd and NYC taxi data have a structure adapted to this type of request. Unlike to Wikipedia, joint request cannot be applied on it. However, a self join could be applied between a parent~child relationship like the descriptions and labels columns. But regarding to the size of Wikipedia, a large resources shared between these columns could crash the system. 

```sql
--Trip data
SELECT f.tolls_amount   
FROM fare_data AS f
JOIN trip_data AS t
ON f.medallion  = t.medallion
WHERE f.surcharge = 10.5;
```

![Jointure](feature/plot/join_trip.png)

In trip data, the results show that ORC followed by Parquet are the suitable formats. Between row formats, CSV optimizes better join requests.

```sql
-- Wikipedia data
SELECT a.region 
FROM imdb_title_principal AS p
JOIN imdb_title_akas AS a
ON a.titleId = p.tconst
WHERE p.tconst LIKE "tt0000001";
```

![Jointure](feature/plot/join_imdb.png)

With imdb, it's difficult to discern the raw performance between file formats. Despite this, ORC and Parquet provides less processing time and eventually CSV in decompression case. In contrast to compression, Parquet seems occur more CPU resource than CSV. This proves again that the small size of data doesn't imply a good precision about the performance comparison.

### Data lookup 

Depending of your data structure, lookup a specific information can generate an overheads of performance according to the filtered type ([primary key or non primary key](https://en.wikipedia.org/wiki/Unique_key#:~:text=A%20non%2Dprimary%20key%20that,in%20a%20single%2Dtable%20select.&text=A%20key%20that%20has%20migrated,unique%20key%20is%20a%20pleonasm.)). The primary key is considered as primary index and implies a very fast lookup when the data contains it. In addition, some file formats embed an index in its definition like ORC and Parquet allowing to retrieve faster an given information. About our datasets, there are only trip data which doesn't have a primary key. Let's see how a lookup could be performed accordingly file formats and the structure of datasets.  

```sql
-- Trip data 
SELECT vendor_id
FROM trip_data
WHERE trip_distance = 49.72;
```

![lookup](feature/plot/lookup_trip.png)

ORC and Parquet are the suitable formats for searching a given information. As usual, row-based file formats consume significant resources especially in CPU time. Between them CSV provides less time and JSON is not appropriate in this case.

In Wikipedia, the data has as primary key `id` columns. We have tested a lookup filtered by this primary key and without. 

```sql
-- Wikipedia data 
--- filter based on a primary key
SELECT type
FROM Wikipedia
WHERE id = "Q19660479";
```

![lookup](feature/plot/lookup_wiki_colprikey.png)

The result show, as usual, that ORC and Parquet are the most efficient. Between row format, AVRO uses less duration time compare to JSON even if it takes lot of CPU resources in decompression case.

Now, let's look at how file formats behave with a none primary key when the lookup is to get all informations from each columns of the data. 

```sql
--- filter based on a none primary key
SELECT * FROM Wikipedia
WHERE array_contains (claims['p1245'].mainsnak.datavalue.`value`, "7956");
```

![lookup](feature/plot/lookup_wiki_npk_all_col.png)

ORC and Parquet is always appropriate. This is in reality explained by the fact they include in their definition an index allowing to find data quickly. Plus, we observe here that Parquet takes less time even though ORC optimizes better the CPU resources. Since Parquet is built by taking account the [complex nested](https://cwiki.apache.org/confluence/display/Hive/Parquet) data, this could be the reason of this difference in this case. 

### Word Count 

In text analysis, word count request is frequently performed. We found more challenging to apply it only in Wikipedia data due to the complex nested structure of some columns. Because with other dataset type, making a word count is similar to `Group by` request.

```sql
CREATE TABLE test AS 
WITH subquery AS (
  SELECT alias_key, d.language AS lan, d.value AS val FROM 
  ( 
    SELECT explode(aliases) AS (alias_key, detail) 
    FROM Wikipedia 
  ) t lateral view explode(detail) detailexploded AS d
),subquery2 AS 
  (
    SELECT concat_ws (",", alias_key, lan, val ) AS col FROM subquery
  ) 
SELECT word, COUNT(*) 
FROM subquery2
lateral view explode(split(col, ',')) lTable AS word
GROUP BY word;
```

![word_count](feature/plot/word_count.png)

The results show that ORC and Parquet optimize well this process. JSON consumes lot of resources. As choice of row formats, AVRO is the most appropriate. 

## Overview

Columnar storage (ORC and Parquet) is remarkable format for reading faster mainly into a specific column. It optimizes efficiently both the duration and CPU resources than row file formats. This proves that column-level statistical index included to their definition plays a significant role. Because it makes irrelevant column blocks skipped in query and improves query performance. 

According to environment test (Hive), ORC is in general more efficient than Parquet. Being the default file format of Hive engine, ORC embeds in addition multiple optimizations properties. In some case specially with semi-structure dataset, Parquet could occur less duration time than ORC. 

Reading request is not very efficient with row file formats. Between them, CSV performs well than other. But within a structure like wikpedia, AVRO is more appropriate even if in decompression it generates lot of CPU resources than JSON. The latter consumes generally a significant processing time in multiple case. Through these results, it does not look an appropriate format for data analysis.

Between compression and decompression case, our result shows that compression case provides more time even though the difference is not significant to compression case. This difference is more remarked in row file formats. This could be due to codec used for compress data as it has slower compression/decompression. Thus, choosing one of these case remains personal regarding to the processing time. In reality, compression case is usually performing since it optimize better the storage space and reading request. The trade-off resides mainly on the choice of compression type by optimizing both the speed and ratio of compression/decompression process. 

## Conclusion

The performance comparison of file formats shows various behave depending to the use case. The columnar file format are globally suitable in anyhow case specially ORC. With row-based storage, the duration time is optimized sometimes by some file like CSV or AVRO. But generally, they consumes significant of CPU resources. About JSON, it does not perform well the reading request and has to be avoided in this case when possible. We hope this tutorial has given you some insight into the performance of the different file formats studied.

## Additional information

- Reading error of AVRO table from Wikipedia data 

With semi-structured data, if you convert data without creating the [Avro schema](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) manually, this can raise sometime an error when reading the table as below. 
 
```
0: jdbc:hive2://zoo-1.au.adaltas.cloud:2181,z>select * from wikimedia_avro limit 2;
Error: java.io.IOException: org.apache.avro.AvroTypeException: Found datasets.record_0, expecting union (state=,code=0)
```

As described above, this error takes from the AVRO schema. It's due to the column type which may be null or not. These type of column must be defined as union of null with the real type of data if it exists. Below, you have an example how the schema should be write it.

```
{
   "type":"record",
   "name":"wiki_avro",
   "namespace":"test",
   "fields":[
      {
       	 "name":"id",
         "type":[
            "null",
            "string"
         ],
         "default":null
      },
      {
       	 "name":"type",
         "type":[
            "null",
            "string"
         ],
         "default":null
      }]
}

```

Eventually, you could get further information about this error from the log Hive server and even a suggested schema for your table.
