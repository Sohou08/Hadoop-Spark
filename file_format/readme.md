
# BIG DATA FILE FORMATS: BENCHMARK STUDY BETWEEN CSV, ORC, JSON, PROTOCOLE BUFFER, AVRO, PARQUET, XML #

Big data ecosystem (Hadoop, Apache) support multiples types of files formats according of the analysis purpose . Thus, the goal of this report is to understand what are the most suitable to:

+	__Row-based storage and column-based storage__

The choice of a big data format need to known whether a row or column-based format is best suited to your objectives. At the highest level, column-based storage is most useful when performing analytics queries that require only a subset of columns examined over very large data sets. If your queries require access to all or most of the columns of each row of data, row-based storage will be better suited to your needs. 
Row stores have the ability to write data very quickly, whereas a column store is awesome at aggregating large volumes of data for a subset of columns.

+	__Perform OLAP or OLTP query__ 

OLTP is an online Transaction Processing system. The main focus of OLTP system is to record the current Update, Insertion and Deletion while transaction. OLAP is online Analytical Processing system. OLAP's main operation is to extract multidimensional data for analysis. 

+	__Be splittable i.e. multiple task can run parallel on parts of file__

Hadoop stores and processes data in blocks you must be able to begin reading data at any point within a file in order to take fullest advantage of Hadoop’s distributed processing. Thereupon, splittable files is suitable so that its massively distributed engine can leverage data locality and parallel processing. 

+   __Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy)__.

That allow to reduce data storage space on disk; increase the performance of lecture and readable on the disc ; also improve the speed of file transfer over the network. For more information about compression.

+  __Streaming and Batch__

Batch processing is where the processing happens of blocks of data that have already been stored over a period of time. For example, processing all the transaction that have been performed by a major financial firm in a week. 
Stream processing allows to process data in real time as they arrive and quickly detect conditions within small time period from the point of receiving the data (e.g. tasks od stream processing is fraud detection).

+	__Support Schema evolution, allowing us to change schema of file__

The schema will store the definition of each attribute and its type. Unless your data is guaranteed to never change, you’ll need to think about schema evolution in order to figure out if your data schema changes over time, does it possible to manage fields as added, deleted or renamed.

Let's describe briefly each file formats and their charactistics 

## **Row-based file format**

### 1. CSV and TSV
CSV and TSV are a text Input Format. CSV represents a lines of comma separated fields with an optional header.
TVS uses TAB as a default fiel delimiter.  
Both are splittable and compressible. They are human-readable and easy to edit manually, processed by almost all existing applications. In terms of comparaison, parsing TSV data is more simple than csv.
They don't manage null value and generally not standart for big data.

   ![csv-tsv](https://user-images.githubusercontent.com/51121757/80370776-b7467800-8888-11ea-9245-d0bc60d8c115.JPG)

### 2. JSON (JavaScript object notation) 
JSON is a text Input Format containing record which might be in any form (string, integer, object,nested data...). It support serialization and deserialization process. 
JSON is compared to XML due to the fact it can store data in hierarchical format. Both are user-readable but JSON is much smaller than XML. There are commonly used in network communication.
They stores metadata with the data, fully enabling schema evolution. However, JSON is not splittable due to the fact it does't contain a special character where we can based to subdivise the text e.g "\n" quote. 
It is widely used file format for NoSQL databases such as MongoDB, Couchbase, Azure Cosmos DB,...

   ![Json versus xml](https://user-images.githubusercontent.com/51121757/80371280-8fa3df80-8889-11ea-867d-2ab16f31fb71.JPG)

### 3. XML 
XML is a input text format. It is not splittable since XML has an opening tag at the beginning and a closing tag at the end. You cannot start processing at any point in the middle of those tags.
As Json,  It is generally used to serialize, encapsulate, and exchange data. 
XML syntax is verbose, especially for human readers, relative to other alternatives ‘text-based’ data transmission formats. The distinction between content and attributes in XML seems unnatural to some and makes designing XML data structures harder.
XML is general less usable on data processing due to the lack of large-scale parallelization of processing (no splittable)

   ![xml](https://user-images.githubusercontent.com/51121757/80371480-e0b3d380-8889-11ea-9ccd-ca8964b9eb22.JPG)

There are other formats at our disposal more suitable in serialization, exchange data as e.g. avro and Protocol Buffers.

### 4. AVRO

AVRO stored in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compacted easily. It attach metadata into their data in each record and it is compressible and splittable. AVRO has an enough capacity to manage the schema evolution (at different time and independently). 
His proprety about serialization and deserialization bring it a very good ingestion performance.
AVRO has a sync marker to separate the block (splittable); typically used to write-heavy workload because rows can be added simply and quickly. 

It is more used in case of streaming processing due to their speed of ingestion. 

   ![e.g. AVRO file](https://user-images.githubusercontent.com/51121757/80033375-8232d200-84e4-11ea-9531-076f72e30bea.JPG)

[Source](https://blog.clairvoyantsoft.com/big-data-file-formats-3fb659903271)
    
### 5. Protocol Buffer
Protocol buffer is language-neutral, an extensible way of serializing structured data for use in communications protocols, data storage, and more. You can easily read it and understand it as an human. The data is fully  type . The schema is needed to generate code and read the data. The documentation can be embedded in the schema. As many format, the data can be read across any language. 
Like AVRO, protocol buffer support the schema evolution.
However, it is not splittable , not compressible and does't support MapReduce.

   ![protocole buffer2](https://user-images.githubusercontent.com/51121757/80370785-bb729580-8888-11ea-8669-c26170bc9f5f.JPG)

[Source](https://blog.eleven-labs.com/fr/presentation-protocol-buffers/)  

## **Column-based file format**

### 6. Parquet

Parquet is a binary file containing  metadata about their content. The column metadata for a Parquet file is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM).
It is splittable and widely used in many environment as ruche, porc, Impala, hive; l’étincelle, SPARK, flink, athena, bigQuery,….
Parquet is very efficient for OLAP query processing as ORC. It is slower to write than other column formats. 
Howerver, It support limited schema evolution.
At present, Hive and Impala are able to query newly added columns, but other tools in the ecosystem such as Hadoop Pig may face challenges.

   ![parquet](https://user-images.githubusercontent.com/51121757/80372035-c3333980-888a-11ea-87af-97425e00c476.JPG)

[Source](https://blog.ippon.fr/2020/03/02/de-limportance-du-format-de-la-donnee-pratique-partie-2-2/) 

### 7. ORC

An ORC file contains groups of row data called stripes, along with auxiliary information in a file footer. At the end of the file a postscript holds compression parameters and the size of the compressed footer.
The default stripe size is 250 MB. Large stripe sizes enable large, efficient reads from HDFS.
The ORC improves performance reading, writing, and processing in Hive (Suitable for OLAP query), lightweight file and compressible (ZLIB by default). It support also the supplementary compression via LZO, Snappy. 
ORC reduce the size of the original data up to 75%. ORC does not support schema evolution

It can’t be load data directly into ORCFILE, increases CPU overhead by increasing the time it takes to decompress the relational data, Specific version (Hive 0.11)

   ![ORC](https://user-images.githubusercontent.com/51121757/80372034-c29aa300-888a-11ea-9b37-0114b5a0c0c2.JPG)

[Source](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) 
  
##  Overview

[Comparison table 1](https://user-images.githubusercontent.com/51121757/80376695-1d83c880-8892-11ea-9614-e460aabe7dd3.JPG)

[Comparison table 2](https://user-images.githubusercontent.com/51121757/80578595-9c970f00-8a00-11ea-96fe-fb4c7468ad78.JPG)


[e.g. File format comparison](https://2s7gjr373w3x22jf92z99mgm5w-wpengine.netdna-ssl.com/wp-content/uploads/2018/05/Nexla-File-Format.png), [e.g. Comparison in terms of reading, space,storage, execution time,...](https://luminousmen.com/post/big-data-file-formats)


[Let's test all these differences practically](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/pratice_test)...






