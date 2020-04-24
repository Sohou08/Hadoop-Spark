
# BIG DATA FILE FORMATS: BENCHMARK STUDY BETWEEN CSV, ORC, JSON, PROTOCOLE BUFFER, AVRO, PARQUET, XML #

Big data ecosystem (Hadoop, Apache) support multiples types of files formats according of the analysis purpose . Thus, the goal of this report is to understand what are the most suitable to:

+	Get read fast 
+	Get written fast 
+	perform OLAP or OLTP query 
+	Be splittable i.e. multiple task can run parallel on parts of file
+	Support Schema evolution, allowing us to change schema of file
+	Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy). That allow to reduce data storage space on disk; increase the performance of lecture and readable on the disc ; also improve the speed of file transfer over the network.

## **Row-based file format**
------
### 1. CSV

CSV is a text Input Format. It represents a lines of comma separated fields with an optional header. CSV is splittable , less compressible file.

__Advantage__ : CS is human-readable and easy to edit manually; provide a straightforward information schema;  processed by almost all existing applications; simple to implement and parse, write performance but slow to read

__Drawback__ : doesn't manage Null value and not standard for Big data

[e.g. Csv file](https://user-images.githubusercontent.com/51121757/80033301-616a7c80-84e4-11ea-80e4-b03bffc27669.JPG)

### 2. JSON (JavaScript object notation) and XML

JSON is a text Input Format and contain a list of objects. It is represented as key-value pairs in partially structured format. It is very smaller in term of size. His kind of storage (row based data ) can be optimized by containing parquets or avro format.
JSON is compared to XML due to the fact it can store data in hierarchical format. Both are user-readable but JSON is much smaller than XML. There are commonly used in network communication.
Many Batches or Stream data processing models natively support JSON serialization and deserialization. It attach metadata into their data in each record. 


__Advantage__ : JSON support hierarchical structures; exchange formats for hot data and cold data warehouses; widely used for NoSQL 

__Drawback__ : Xml is less readable compared to Json

[e.g. Json versus xml](https://user-images.githubusercontent.com/51121757/80033313-662f3080-84e4-11ea-8e18-35addbcee12a.JPG)

### 3. AVRO

AVRO is highly splittable and compressible. It also described as a data serialization system and deserialization. The schema is stored in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compacted easily. This following propriety allow it to be supported in many different programming languages ((python, C, C++, …) .
It’s a good candidate for data storage in Hadoop ecosystem. AVRO has an enough capacity to manage the schema evolution (at different time and independently).

__Advantage__ : His proprety about serialization and deserialization bring it a very good ingestion performance.
AVRO has a sync marker to separate the block (splittable); typically used to write-heavy workload because rows can be added simply and quickly. His key feature is the robust support for data schema that changes over time (schema evolution) , average read/write performance. It’s the best choice if your data schema change frequently (files can renamed, added, deleted, while old files can still be read with the new schema)

__Drawback__ : heavy to read

[e.g. AVRO file](https://user-images.githubusercontent.com/51121757/80033375-8232d200-84e4-11ea-9531-076f72e30bea.JPG):
    [Source](https://blog.clairvoyantsoft.com/big-data-file-formats-3fb659903271)

## **Column-based file format**

### 4. Parquet

Parquet is a binary file containing  metadata about their content. The column metadata for a Parquet file is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM). It’s slow to write but very fast to read. It’s compatible with all MapReduce interfaces such as Java, Hive, Pig. Being appropriate to hive, it will be suitable for OLAP queries.

__Advantage__ : fastest for reading workflow but very slow to write, well known in nested data structure complexes, support compression mostly with snappy algorithm. It also splittable.

__Drawback__ : immutable, does not support schematic evolution. 

[e.g. Parqet file](https://user-images.githubusercontent.com/51121757/80033382-852dc280-84e4-11ea-8fbb-f29ef9d06ff1.JPG):
     [Source](https://netjs.blogspot.com/2018/07/parquet-file-format-in-hadoop.html) 

### 5. ORC

The ORC format can only support Hive (Suitable for OLAP query), lightweight file and always in compression. It support also the supplementary compression via the use of LZO, Snappy. It's a column storage data with stripe. ORC reduce the size of the original data up to 75%.

__Advantage__ : ORC improves performance reading, writing, and processing in Hive; Optimize the Cost storage
 (due to his type of storage)
__Drawback__ : Can’t be load data directly into ORCFILE, increases CPU overhead by increasing the time it takes to decompress the relational data, Specific version (Hive 0.11)

[e.g. ORC file](https://user-images.githubusercontent.com/51121757/80036471-b066e080-84e9-11ea-9525-2a1a4c26104d.JPG):
     [Source](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) 

### 6. Protocol Buffer

It is a binary encoding format that allows you to specify a schema for your data using a specification language. It used for serialize of structured data.

__Advantage__: ?

__Drawback__: No splittable, Less compressible and don’t support MapReduce

[e.g. Protocole buffer](https://user-images.githubusercontent.com/51121757/80033392-8828b300-84e4-11ea-84df-01326a4b56ac.JPG):
       [Source](https://blog.eleven-labs.com/fr/presentation-protocol-buffers/) 
                                                                                                                                                                 
##  Overview
CSV and Json are less suitable for stockage or data analysis.  However Json is very efficient format to share data into a network communication. The majority of the community big data consider parquet, ORC and avro the more optimized in this domain due to their splittability, compression support. 
In practice, row-oriented storage layouts are well-suited for OLTP-like workloads whereas column-oriented storage layouts are well-suited for OLAP-like workloads (e.g., data warehouses) which typically involve a smaller number of highly complex queries over all data (possibly terabytes). Parquet and ORC file formats are widely adopted options on query engine mainly of OLAP query.


#### Tips
__Binary_format__ : This is designed for efficient data processing by the CPU

__Compression__:  Compressed data reduces the reading time,thus the delay of data processing. Snappy is optimized for speed of execution, especially when decompressing data

__Serialization__ : Serialization is a process of converting a file into a stream of bytes.
 





                                                                                                                    







