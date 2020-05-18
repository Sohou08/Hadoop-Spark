
## File formats comparison: Practical test

After a brief review of file format comparison ([theorical comparison](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format)), it seems relevant to practice some principle embodied on the literature. Those tests will allow to check some statements made on the different types of formats (CSV, JSON, AVRO,ORC,...) through hive database, different types of data and specific request.

## Requirement Environment

  * __Characteristics of the cluster__
  
  * __Database Choice__
  
Apache Hive is an SQL-like software used with Hadoop to give users the capability of performing SQL-like queries on it’s own language, HiveQL, quickly and efficiently. Inversly to Hadoop, Hive supports different type OLAP or OLTP query. Using hadoop, it's fully take advantage of the distributed data processing
and supports many file formats (ORC, CSV, AVRO, Parquet,...).

  * __datasets types__
  
      * document text
      * Unstructured data (wikipedia data)
      * Classic dataset (IMDB dataset)
      * Time series data (Taxi driver)
  
  * __Requests types__
  
      * Space utilization per format
      * Ingestion latency per format
      * storage space
      * Random data lookup
      * Update data or schema 
      * Aggregation (min, max,mean)
      * Map join

## Query and Results



## Conclusion




## Reference 
https://luminousmen.com/post/big-data-file-formats
