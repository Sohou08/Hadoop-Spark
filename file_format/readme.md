

# FILE COMPARISON BETWEEN CSV, ORC, JSON, PROTOCOLE BUFFER, AVRO, PARQUET, XML #

The Goal of this report is to understand what they are the most suitable to:
+	Get read fast (mainly the case of row based storage)
+	Get written fast (case of row based storage)
+	perform OLAP or OLTP query 
+	Be splittable i.e. multiple task can run parallel on parts of file
+	Support Schema evolution, allowing us to change schema of file
+	Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy). That allow to reduce data storage space on disk, increase the performance of lecture and readable on the disc and also the transfer speed in the network.



     **Row based file format**
------
## 1. CSV

It’s commonly used to exchange tabular data between systems using plan text, splittable , less compressible file.

Advantage : human-readable and easy to edit manually; provide a straightforward information schema;  processed by almost all existing applications; simple to implement and parse, write performance but slow to read

Drawback : don’t manage Null value and not standard for Big data

## 2. JSON (JavaScript object notation) and XML

Json is represented as key-value pairs in partially structured format. It store data to the hierarchical format. It is self-describing and readable. It is smaller in term of size. Json is often used in network communication; also in serialize of deserialize data. His kind of storage (row based data ) can be optimized by containing parquets or avro format.
Many Batches or Stream data processing models natively support JSON serialization and deserialization. It attach metadata to the data in each record. 

Advantage : It support hierarchical structures and a lists of objects; widely used for NoSQL 

Drawback : Xml is less readable compared to Json

# 3 AVRO

It is a row-based format that is highly splittable and compressible. It also described as a data serialization system and deserialization. The schema is stored in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and compact. This following propriety allow it to can be supported in many different programming languages ((python, C, C++, …) .
It’s a good candidate for data storage in Hadoop ecosystem. Has Enough capacity to manage the schema evolution (at different time and independently), can be used in streaming data.

Advantage : fast data serialization and deserialization which can deliver very good ingestion performance, has a sync marker to separate the block (splittable), typically used to write-heavy workload because rows can be added simply and quickly. His key feature is the robust support for data schema that changes over time (schema evolution) , average read/write performance. It’s the best choice if your data schema change frequently (files can renamed, added, deleted, while old files can still be read with the new schema)

Drawback : 
