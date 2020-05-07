
# BENCHMARK STUDY OF DIFFERENT FILE FORMATS: CSV, JSON, XML PROTOCOL BUFFER, AVRO, PARQUET, ORC #

There exist a different types of files formats according of the analysis purpose. These formats have their own pros and cons depending upon the use cases what is important into take account when choosing them. Some of them are more relevant of certain analysis (e.g BI), data exchange over network, web application; batch or stream processing;...

For instance, CSV is a file format which is very understandably. In case of web API or web development,  JSON is the privilege even in some case XML could do the same work. The advantage of XML is his standardize and specific format which occurs. As for AVRO and Protocol buffer, they are considered a favor format in streaming processing. In addition, protocol buffer is the stand of CRI (Container Runtime Interface) in Kubernetes and also to gRPC service. In Business Intelligence , there are some file format as OCR and parquet which occurs an efficient analysis due to generally to their column storage type. As you see, each file format include his own specify and interest according to the use cases. 

Thus, we would be covering firstly various such aspects of storage type; compression required; splittable; batch or stream process and the schema evolution then end up with detailed description of each file format named above.

## **Consideration to be taken into account for choosing a file format**

The choice of a big data format need to known whether a row or column-based format is best suited to your objectives. In row based storage, the data is stored row by row, such that the first column of a row will be next to the last column of the previous row. So, these kind of architecture are appropriate to add data easily and quickly too. It is suitable for situations where the entire row of data needs to be processed simultaneously. That is commonly used for Online Transactional Processing (OLTP). OLTP systems process all kinds of queries (Read, Insert, Update and Delete)

Inversely, in column-based storage, the data is stored such that each row of a column will be next to other rows from that same column. That's most useful when performing analytics queries that require only a subset of columns examined over very large data sets called OLAP (OnLine Analytical Processing) query. OLAP is an approach designed to quickly answer analytics queries involving multiple dimensions. This approach has played a critical role in business intelligence analytics, especially in regards to Big Data. The data aggregation and pre-calculation enabled by OLAP query, have proven to be a great way to avoid the excessive processing times of lecture. Because it lets you ignore all the data that doesn't apply to a particular query. 

By contrast, if you were working with a row-oriented storage and you wanted to know, the average population density in cities with more than a million people, your query would access each record in the table (meaning all of its fields) to get the information from the two columns whose data you needed. That would involve a lot of unnecessary disk seeks and disk reads, which also impact performance. 
Speaking of disk reads, columnar databases could boost performance in another way by reducing the amount of data that needs to be read from disk. Thus, row-oriented databases are still the best choice for OLTP applications, while column-oriented databases are generally better for OLAP. 


*  __Splittable__

In case of distributed environment, it is important to have a file that can be divide into several pieces. That's the case of Hadoop which stores and processes data in chunk. So, be able to begin reading data at any point within a file is helpful in order to take fullest advantage of Hadoop’s distributed processing ([document](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)). That why a splittable is very useful in this kind of case. Once the data is splittable and partitioned in different node, this kind of system could participate of lecture optimized since you only read the needed data. Unfortunately, JSON and XML which doesn't take advantage of that because they are not splittable.

* __Compression (Bzip2, LZO, Sappy,...)__

Within a distributed system, data being stored in disc, this one is often slower to access that why it seems relevant to compress data. That allows to reduce data storage space on disk; increase the performance of lecture and readable on the disc ; also improve the speed of file transfer over the network. In term of optimized lecture, the data partition is tip important allowing to read only the data needed. 
In addition, column based storage is very fast and better to compress than row based storage since you're storing similar pieces of data together.
Generally, text formats types are more compressible than a binary format. cest du a lorigine a la taille des fichiers text. Par example avec un ficher text comme XML la compression reduit enormenet la taille comparer a un fichier video
 File having a binary data take less advantage according to the data which is compressed by default. Indeed, 

There are many kind of compression algorithm which differ according of the ratio of compression, speed, performance, splittable,... [For more information about compression](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/compression). 

* __Streaming and Batch__

Stream processing allows to process data in real time as they arrive and quickly detect conditions within small time period from the point of receiving the data. For example, processing all the transaction that have been performed by a major financial firm each day for processing fraud detection. 
Over a period of time (day,week,...) occurring a block of data, batch processing will be subsequently process for other purpose.

* __Support Schema evolution, allowing us to change schema of file__

The schema will store the definition of each attribute and its type. Unless your data is guaranteed to never change, you’ll need to think about schema evolution in order to figure out if your data schema changes over time, does it possible to manage fields as added, deleted or renamed. 

+	__FILE FORMAT DESCRIPTION__

## 1. CSV 

CSV is a text Input Format and use newline as the record delimiter with an optional header. It is human-readable and easy to edit manually, processed by almost all existing applications. Le CSV ne contient pas dans ces data le schema du coup 

CSV is not splittable itself cause it doesn't contain a specific character where we could based to split the text while remaining relevant. Most of case in big data, it often seems like used CSV file but it's a TSV strictly speaking. Indeed, CSV file format is not fully standardized. The field delimiters could be for example, colon or semicolons which become difficult to parse when the field itself may also contain these kind of delimiter or even embedded line breaks. In addition, the field data could be enclosed by a quotation marks and it can also include a escape characters or escape sequence which occurs many complexity of parsing. 
Whereas TSV doesn't not support a escape character and is generally delimited by a TAB (sometime a comma) allowing to distinguish easily each column. Thus, TSV is more simple to parse than CSV.

   * __Advantage__
       * compressible
       * batch/streaming processing      
   * __Drawback__
       * doesn't support null value 
       * reading require programs to handle the escape character
       * No schema evolution
       * isn't a specific format because every one has his own interpretation in CSV
   * __Ecosystems__ 
       * supported by a wide range of applications (Hadoop, spark, kafka,...)
       * Less recommended but it is one of most popular used format
 
  [source](https://github.com/eBay/tsv-utils/blob/master/README.md)

### 2. JSON (JavaScript object notation) 

JSON is a text Input Format containing record which might be in any form (string, integer, booleans, array, object, key-value, nested data...). By virtue of being both human- and machine-readable, and comparatively simple to implement support for in other languages, JSON quickly moved beyond the web page, and into software everywhere. It support serialization and deserialization process.
JSON cannot be splittable compare to JSON lines. This one essentially consists of several lines where each individual line is a valid JSON object, separated by newline character `\n`. That's take an advantage to be splittable compared to the standard format.
JSON lines represents too an aesthetic advantage compare to JSON standard by separating each line by a line break. 

* __Advantage__
    * simple syntax. It can be opened by any text editor and supported natively by many languages. There exist too tool enabling to validate the schema.  
    * favor format for web applications
    * compressible 
    * data exchange format
    * Support batch/streaming processing
    * store metadata with data and support schema evolution
    * fully typed performing the compiler optimization.
    * lightweight text-based format in comparison to XML 
* __Drawback__
    * No splittable 
    * Lack indexing 
* __Ecosystems__ 
    * is privilege exchange format for NoSQL databases. As example, we could notice BJSON used in MongoDB database. BSON’s binary structure encodes type and length information, which allows it to be parsed much more quickly. However, that doesn’t mean you can’t think of MongoDB as a JSON database (JSON text). In addition, this structured way of JSON text is easier to modify there than in structured database as Postgres where you'd need to extract the whole document for changing.
    * is a language used in CraftQL which bring a typage par dessus
    * is privilege format for web applications.
    
[Example of JSON](https://docs.microsoft.com/bs-latn-ba/azure/hdinsight/hadoop/using-json-in-hive):
~~~ {r}
       {
          "StudentId": "trgfg-5454-fdfdg-4346", # a string
          "Grade": 7, # an integer
          "StudentClassCollection": [
                {
                    "ClassId": 89084343, # a integer
                    "Score": NULL,
                    "amount": 12.60 # a float
                    "PerformedActivity": false # a boolean
                }, # an Object
                                   ]
        }
~~~ 

[Example of JSON lines](https://hackernoon.com/json-lines-format-76353b4e588d):
~~~{r}
{"id":1,"father":"Mark","mother":"Charlotte","children":["Tom"]}
{"id":2,"father":"John","mother":"Ann","children":["Jessika","Antony","Jack"]}
{"id":3,"father":"Bob","mother":"Monika","children":["Jerry","Karol"]}
~~~

### 3. XML 

XML is a input text format. It makes it possible to define languages which are more or less complex but which make it possible to establish file format standards for exchanges between applications. As JSON,  XML is generally used to serialize, encapsulate, and exchange data. XML syntax is verbose, especially for human readers, relative to other alternatives ‘text-based’ formats. 

* __Advantage__
    * data exchange format
    * Support batch/streaming processing
    * store meta data with data and support schema evolution
    * Syntax verbose enabling to have a good ratio of compression compare to JSON file
* __Drawback__
    * No splittable : XML has an opening tag at the beginning and a closing tag at the end. You cannot start processing at any point in the middle of those tags.
    * increase in data size and processing time because the document size is often bulky and with big files, the tag structure makes it huge and complex to read which occurs slow process in parsing, leading also to slower data transmission
    * parsing escape character of XML is complicate because it has many character which might be : " " ' ' < < > > & &.
    the alternative used in these cases is often HTML which only has a simple character (< < > >).
     
* __Ecosystems__
    * practically no used in any environment cause it bring many disadvantage in term of storage or exchange
    
~~~ {r}
<?xml version="1.0" encoding="UTF-8"?>
<scientists>
  <!-- Some important physicists -->
  <scientists id="phys0">
    <first name="Galileo" name="Galilei"/>
    <dates of birth="1564-02-15" death="1642-01-08"/>
    <contributions>Relativity of movement &amp; heliocentric system</contributions>
  </scientists>
~~~ 
    
  [source](https://github.com/facebookarchive/hadoop-20/blob/master/src/hdfs/hdfs-default.xml)
       

### 4. AVRO

AVRO stored his schema in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compacted easily. It attach metadata into their data in each record. 

* __Advantage__
    * splittable (AVRO has a sync marker to separate the block)
    * good file format for data exchange. 
    * data storage is very compact, fast and efficient 
    * Compressible (at different time and independently)
    * Highly support schema evolution
    * supported batch and most streaming engine
    * fast serialization process
* __Drawback__
    * no readable by human
    * limited environment since all languages don't occur a encoded or decoded.
    
* __Ecosystems__ 
    * widely used in many application (kafka, spark, Cassandra )
      
   ![e.g. AVRO file](https://user-images.githubusercontent.com/51121757/80033375-8232d200-84e4-11ea-9531-076f72e30bea.JPG)

[Source](https://blog.clairvoyantsoft.com/big-data-file-formats-3fb659903271)
    
### 5. Protocol Buffer

Protocol buffer is language-neutral, an extensible way of serializing structured data for use in communications protocols, data storage, and more. You can easily read it and understand it as an human. The schema is needed to generate code and read the data. 
   
* __Advantage__
    * data fully typed
    * support schema evolution
    * Support batch/streaming
    * used to serialize data as AVRO
    * validate the structure. Having a predefined and larger set of data types, messages serialized on Protobuf can be automatically validated by the code that is responsible to exchange them.
* __Drawback__
    * No splittable and No Compressible
    * doesn't support Map reduce
    * need a reference file to be decoded 
    * Protocol Buffers are not designed to handle large messages. Since it doesn't support random access. You'll have to read the whole file, even if you only want to access a specific item.
* __Ecosystems__ 
    * Kubernetes environment
    * support RPC (Server RPC interfaces can be declared as part of protocol files)
    * ProfaneDB is a database for Protocol Buffer objects.
  
~~~{r}
syntax = "proto3";
  message Mymessage {
  int32 id = 1;
  string first_name = 2;
  bool is_validated = 3;
                    } 
~~~

## **Column-based file format**

### 6. Parquet

Parquet is a binary file containing  metadata about their content. The column metadata for a Parquet file is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM). 

 * __Advantage__
     * splittable and compressible by default
     * organizing by column allows for better compression, as data is more homogeneous.
     * efficient for OLAP query
     * support schema evolution
     * No human readable
     * Support batch/streaming processing
 * __Drawback__
     * difficult to make a update a parquet table unless you delete and recreate it again.
 * __Ecosystems__
     * give a efficient analysis in case of BI
     * very fast to read in Spark environment
     * aside Spark and Impala; Arrow, Drill are part of the most compatible platforms with parquet.
     
     
   ![parquet](https://user-images.githubusercontent.com/51121757/80372035-c3333980-888a-11ea-87af-97425e00c476.JPG)

[Source](https://blog.ippon.fr/2020/03/02/de-limportance-du-format-de-la-donnee-pratique-partie-2-2/) 

### 7. ORC

ORC file contains groups of row data called stripes, along with auxiliary information in a file footer. At the end of the file a postscript holds compression parameters and the size of the compressed footer.
The default stripe size is 250 MB. Large stripe sizes enable large, efficient reads from HDFS.

  * __Advantage__
      * compressible
      * reduce the size of the original data up to 75%
      * OLAP (more efficient)/OLTP
      * Supports batch processing
      * supports the use of zlib, LZO, or Snappy to provide further compression
   * __Drawback__
      * can’t be load data directly into ORCFILE
      * does not support schema evolution
   * __Ecosystems__ 
      * give a efficient analysis in case of BI
      * improves performance reading, writing, and processing in Hive
      * Impala currently does not support ORC format
      

   ![ORC](https://user-images.githubusercontent.com/51121757/80372034-c29aa300-888a-11ea-9b37-0114b5a0c0c2.JPG)

[Source](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) 

## __Conclusion__

In general, reading or writing fast according to the type of storage is very related to the kind of query tied to the property of each file format. Usually, row-oriented databases are well-suited for OLTP-like workloads whereas column-oriented systems is well suited of OLAP query. Column data in column based storage is of uniform type; therefore, it will be more easy and better to compress compared a row based storage. 

In term of comparison, JSON, XML are less used in data processing regarding to their lack of splittable. JSON line is however most used in these case. Most of case , XML and JSON are used to exchange data over network and serialization  and well known to support schema evolution as AVRO. This one is considered actually the privilege format of streaming processing due to fact his ability to support schema evolution. In addition, it is splittable and compressible which occurs it simultaneously the file format appropriate in big data ecosystem. That what protocol buffer lack about splittable, compressible. However, being a language fully typed as JSON , it bring better performance for compiler optimization.

The last two formats (ORC and parquet) are optimized the cost storage by avoiding repeated data. They are very efficient for Business Intelligence and very compatible with Impala and Hive ecosystems.
In general, all these differences are not statics. It's can evolve and differ according to the use cases. Some of them could be fit in order to respond a specific purpose.
  
##  Overview

Types|CSV | JSON | XML| AVRO| Protocol Buffer | Parquet | ORC 
--- | --- | --- | --- | --- |--- |--- |---
[Format (text/binary)]()| text| text| text| text stored in JSON and data is binary format| text| binary|binary|
[Row store]()| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:|:heavy_check_mark:| :heavy_check_mark:| :x:|:x:|
[Column store]()| :x:| :x:| :x:|:x:| :x:| :heavy_check_mark:|:heavy_check_mark:|
[Splittable]()| :x:| :x: No the case of JSON line| :x:|:heavy_check_mark:| :x:| :heavy_check_mark:|:heavy_check_mark:|
[Compression]()| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:|:heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:|:heavy_check_mark:|
[Data type]()| String, integer,boolean,...| any type (string, boolean,object,array,...| any type of data|text for the schema and binary for data| any type| binary|binary|
[Ecosystems]()| most popular used format| API and development web| practically no used in any environment|kafka,spark,... | Kubernetes,ProfaneDB, doesn't support MapReduce| Impala, HIve,BigQuery, Spark,...|Hive,didn't support by Impala|
[Schema evolution]()| :x:| :heavy_check_mark:| :heavy_check_mark:|:heavy_check_mark:| :x:| :heavy_check_mark:|:x:|
[Suitable for OLAP or OLTP]()| OLTP| OLTP| OLTP|OLTP| :x:| OLAP|OLAP|
[Batch/stream]()|:heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:|:heavy_check_mark:very efficient in streaming case | :heavy_check_mark: very efficient in streaming case|:heavy_check_mark:|:heavy_check_mark:|
[Typed data]()|:x:| :heavy_check_mark:| :x:|:x:| :heavy_check_mark:|:x:|:x:|

[Documentation]()
[e.g. File format comparison](https://2s7gjr373w3x22jf92z99mgm5w-wpengine.netdna-ssl.com/wp-content/uploads/2018/05/Nexla-File-Format.png), [e.g. Comparison in terms of reading, space,storage, execution time,...](https://luminousmen.com/post/big-data-file-formats)


[Let's test all these differences practically](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/pratice_test)...
