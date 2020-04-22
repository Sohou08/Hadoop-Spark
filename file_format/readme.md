

# BIG DATA FILE FORMATS: BENCHMARK STUDY BETWEEN CSV, ORC, JSON, PROTOCOLE BUFFER, AVRO, PARQUET, XML #

The ecosystem big data (Hadoop, Spark) support multiples kind of file format which can be used depending the

The Goal of this report is to understand what they are the most suitable to:
+	Get read fast (mainly the case of row based storage)
+	Get written fast (case of row based storage)
+	perform OLAP or OLTP query 
+	Be splittable i.e. multiple task can run parallel on parts of file
+	Support Schema evolution, allowing us to change schema of file
+	Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy). That allow to reduce data storage space on disk, increase the performance of lecture and readable on the disc and also the transfer speed in the network.



## **Row-based file format**
------
### 1. CSV

It’s commonly used to exchange tabular data between systems using plan text, splittable , less compressible file.

Advantage : human-readable and easy to edit manually; provide a straightforward information schema;  processed by almost all existing applications; simple to implement and parse, write performance but slow to read

Drawback : don’t manage Null value and not standard for Big data

### 2. JSON (JavaScript object notation) and XML

Json is represented as key-value pairs in partially structured format. It store data to the hierarchical format. It is self-describing and readable. It is very smaller in term of size. Json is often used in network communication; also in serialize of deserialize data. His kind of storage (row based data ) can be optimized by containing parquets or avro format.
Many Batches or Stream data processing models natively support JSON serialization and deserialization. It attach metadata to the data in each record. 

__Advantage__ : JSON support hierarchical structures and a lists of objects; widely used for NoSQL 

__Drawback__ : Xml is less readable compared to Json

### 3 AVRO

AVRO is highly splittable and compressible. It also described as a data serialization system and deserialization. The schema is stored in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compact easily. This following propriety allow it to be supported in many different programming languages ((python, C, C++, …) .
It’s a good candidate for data storage in Hadoop ecosystem. AVRO has an enough capacity to manage the schema evolution (at different time and independently).

__Advantage__ : His proprety about serialization and deserialization bring it a very good ingestion performance.
AVRO has a sync marker to separate the block (splittable); typically used to write-heavy workload because rows can be added simply and quickly. His key feature is the robust support for data schema that changes over time (schema evolution) , average read/write performance. It’s the best choice if your data schema change frequently (files can renamed, added, deleted, while old files can still be read with the new schema)

__Drawback__ : 

## **Colunm-based file format**

### 4. Parquet

Parquet is a binary file containing  metadata about their content. The column metadata for a Parquet file is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM). It’s slow to write but very fast to read. It’s compatible with all MapReduce interfaces such as Java, Hive, Pig.

__Advantage__ : fastest for reading workflow but very slow to write, well known in nested data structure complexes, support compression mostly with snappy algorithm. It also splittable.

__Drawback__ : immutable, does not support schematic evolution. 

### 5. ORC

The ORC format can only support Hive (Suitable for OLAP query), lightweight file and always in compression. It support also the supplementary compression via the use of LZO, Snappy. It's a column storage data with stripe. ORC reduce the size of the original data up to 75%.

__Advantage__ : ORC improves performance reading, writing, and processing in Hive; Optimize the Cost storage
 (due to his type of storage)
__Drawback__ : Can’t be load data directly into ORCFILE, increases CPU overhead by increasing the time it takes to decompress the relational data, Specific version (Hive 0.11)

### 6. Protocol Buffer

It is a binary encoding format that allows you to specify a schema for your data using a specification language. It used for serialize of structured data.
__Advantage__: 

__Drawback__: No splittable, No compressible and don’t support MapReduce

Summary
ORC, parquet and Avro could be move between differents node 


