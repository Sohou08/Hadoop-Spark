
# BENCHMARK STUDY OF DIFFERENT FILE FORMATS: CSV, JSON, XML, PROTOCOL BUFFER, AVRO, PARQUET, ORC #

There are different types of files formats according of the analysis purpose. Each format has its own pros and cons depending upon the use cases. This is really important to take account when choosing a specific format type. There is some type of format which is more relevant in certain analysis as e.g Business Intelligence, data exchange over network, web application, batch or stream processing,...

For instance, CSV format is very understandably. In case of web API or web development, JSON is privileged while in some cases XML could do the same work. The latest format type has an huge advantage when it's coming to standardizing. In terms of streaming processing, AVRO and Protocol buffer are a privileged file format regarding their good ingestion. In addition, protocol buffer is the stand of gRPC (Remote procedure call) service in Kubernetes environment. There are some file format like ORC and parquet which occur an efficient analysis mainly in Business Intelligence due to their column storage type. Finally, each file format includes its own specificities and advantages according to the use cases. 

In this description, we would like to cover firstly these various considerations to keep in mind when choosing one format type (storage type, queries types, batch/versus streaming,...), further we will end up with detailed descriptions of each file format named above.

## [**Consideration to be taken into account for choosing a file format**]()

The first consideration when selecting a big data format might be whether row or column-based format is best suited to your objectives. In row based storage, the data is stored row by row, such that the first column of a row will be next to the last column of the previous row. So, these kind of architecture are appropriate to add data easily and quickly. It's also suitable in case where the entire row of data needs to be processed simultaneously. This is commonly used for Online Transactional Processing (OLTP). OLTP systems process all kinds of queries (Read, Insert, Update and Delete). It's very useful as query to manage day to day transactions of an organization. OLTP transactions are usually very specific in the task they perform, and usually involve a single record or a small selection of records. The main emphasis for OLTP systems is focus on very fast query processing, maintaining data integrity in multi-access environments and an effectiveness measured by number of transactions per second. 


Inversely, in column-based storage, the data is stored such that each row of a column will be next to other rows from that same column. That is most useful when performing analytics queries that require only a subset of columns examined over very large data sets called OLAP (OnLine Analytical Processing) query. OLAP is an approach designed to quickly answer analytics queries involving multiple dimensions. This approach has played a critical role in business intelligence analytics, especially regarding to Big Data. The data aggregation and pre-calculation enabled by OLAP query, have proven to be a great way to avoid the excessive processing times of lecture. Because it lets you ignore all the data that doesn't apply to a particular query. 

By contrast, if you were working with a row-oriented storage and you wanted to know, the average population density in cities with more than a million people, your query would access each record in the table (meaning all of its fields) to get the information from the two columns whose data you needed. That would involve a lot of unnecessary disk seeks and disk reads, which also impact performance. 
Speaking of disk reads, columnar databases performs well the process by reducing the amount of data that needs to be read from disk. Thus, row-oriented databases are still the best choice for OLTP applications, while column-oriented databases are generally better for OLAP.

[Example of row and column based storage]()
![2](https://user-images.githubusercontent.com/51121757/81609463-db00d680-93cf-11ea-9b11-786b494e0bd7.JPG)

*  __Splittable__

In case of distributed environment, it is important to have a file that can be divided into several pieces. That's the case of Hadoop which stores and processes data in chunk. So, be able to begin reading data at any point within a file is helpful in order to take the fullest advantages of Hadoop’s distributed processing ([documentation](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)). 
That's why it is important to take account the splittable for scalable task parallel programming. This architecture of partitioned data could participate on the optimized lecture since you only read or process the block of data which are requisite. 
Unfortunately, JSON and XML which doesn't take advantage of that because they are not splittable.

* __Compression (Bzip2, LZO, Sappy,...)__

Within a distributed system, data are stored in disc which is often slower to access so it seems relevant to compress data. Compression enables to reduce data storage space on disk; increase the performance of lecture and readable on the disc; also improve the speed of file transfer over the network. The column based storage type brings a very fast and better compression than row based storage cause you're storing similar pieces of data together.
In addition, text formats types are more compressible than a binary format. It could be due to their file size. For instance, compress an XML format which is verbose and might have a repeated sequence, is more reducible compare to video file.
There are many kind of compression algorithms which differ according of the ratio of compression, speed, performance, splittable,... [For more information about compression](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/compression). 

* __Streaming and Batch__

Stream processing allows to process data in real time as they arrive. It quickly detect conditions within small time period from the point of receiving the data. For example, processing all the transaction that have been performed by a major financial firm each day for processing fraud detection. Over a period of time (day, week,...) occurring a block of data, batch processing could be subsequently process for other purpose.

* __Support Schema evolution, allowing us to change schema of file__

The schema will store the definition of each attribute and its types. Unless your data is guaranteed to never change, you’ll need to think about schema evolution in order to figure out if your data schema changes over time. In other word, schema evolution allows you to update the schema used to write new data while maintaining backwards compatibility with the schema of your old data

## [**File format description**]()

### 1. CSV 

CSV is a text input format and use newline as the record delimiter with an optional header. It is human-readable and easy to edit manually, processed by almost all existing applications. CSV doesn't support schema evolution but sometime the header is optionally considered like the schema of the data.
CSV is not splittable because it doesn't contain a specific character where you could based to split the text while remaining relevant. Most of case in big data, CSV seems to be used for processing but that's generally TSV format which is used strictly speaking. 
Indeed, CSV file format is not fully standardized. The field delimiters could be for example, colon or semicolons which become difficult to parse when the field itself may also contain these kind of delimiter or even embedded line breaks. In addition, the field data could be enclosed by a quotation marks which is sometime not relevant for knowing the begin or end of the text. Finally, It include an escape character which occurs many complexity of parsing. 
Inversely, TSV is generally delimited by a TAB or a comma sometime enabling to distinguish easily each field. Its major advantage is bound to the fact that it haven't an escape character. According these characteristics, TSV is usually more simple to parse than CSV and mainly used in big data case.

   * __Advantage__
       * CSV is compressible and supports batch/streaming processing      
   * __Drawback__
       * It doesn't support null value 
       * Parsing and reading a CSV format require a programs in order to handle the escape character
       * CSV doesn't support schema evolution
       * It's not a specific format because everyone has its own interpretations
   * __Ecosystems__ 
       * It is supported by a wide range of applications (Hadoop, spark, kafka,...)
       * Being generally less recommended, it is one of the most popular formats used
~~~{r}
Player Name,Position,Nicknames,Years Active
Skippy Peterson,First Base,"""Blue Dog"", ""The Magician""",1908-1913
Bud Grimsby,Center Field,"""The Reaper"", ""Longneck""",1910-1917
Vic Crumb,Shortstop,"""Fat Vic"", ""Icy Hot""",1911-1912
~~~
  [source](https://www.computerhope.com/issues/ch001356.htm)

### 2. JSON (JavaScript object notation) 

JSON is a text input format containing record which might be in any form (string, integer, booleans, array, object, key-value, nested data...). Being human and machine readable, JSON is relatively lightweight format and privileged in web application. It widely supported by many software and comparatively simple to implement in multiples languages. JSON supports serialization and deserialization process. Its major inconvenience is related to its lack of splittable. 
The alternative used format is generally a JSON lines which is splittable. It contains several lines where each individual line is a valid JSON object, separated by newline character `\n`. Its main difference compare to the standard format is related to an aesthetic advantage since JSON line separate objects by a line break. This make you sure that the next line is a new objects so it will be easy to be used as reference for splitting data. 

* __Advantage__
    * JSON is a simple syntax. It can be opened by any text editor
    * There are many tools enabling to validate its schema (e.g.JSON-Spec python library)
    * It is a privileged format in web applications
    * JSON is compressible and used as data exchange format
    * It supports batch and streaming processing
    * It stores metadata with data and supports schema evolution
    * JSON is lightweight text-based format in comparison to XML 
* __Drawback__
    * JSON is not splittable and Lack indexing 
* __Ecosystems__ 
    * It is privileged format for web applications and exchange format for NoSQL databases
    For example, in case of MongoDB, the use of BSON is noticed instead the standard format. BSON’s binary structure encodes type and length information, which allows it to be parsed much more quickly than the standard format. However, that doesn’t mean you can’t think of MongoDB as a JSON database (JSON text). In addition, the architecture of JSON text is easier to modify for instance in MongoDB than in structured database as Postgres where you'd need to extract the whole document for changing.
    * It is a used language in GraphQL by bringing also a data fully typed. 
    GraphQL is a query language for an API. Instead of working with rigid server-defined endpoints, you can send queries to get exactly the data you are looking for in one request ([documentation](https://www.howtographql.com/basics/1-graphql-is-the-better-rest/)).
          
[Example of JSON](https://docs.microsoft.com/bs-latn-ba/azure/hdinsight/hadoop/using-json-in-hive):
~~~ {r}
       {
          "StudentId": "trgfg-5454-fdfdg-4346", # a string
          "Grade": 7, # an integer
          "StudentClassCollection": [
                {
                    "ClassId": 89084343, # an integer
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

XML is a input text format. It makes it possible to define languages which are more or less complex but which make it possible to establish file format standards for exchanges between applications. As JSON, XML is generally used to serialize, encapsulate, and exchange data. XML syntax is verbose, especially for human readers, relative to other alternatives ‘text-based’ formats. 

* __Advantage__
    * XML is a data exchange format and supports batch/streaming processing
    * It stores meta data with data and supports schema evolution
    * Being a verbose format, It provides to have a good ratio of compression compare to JSON file
* __Drawback__
    * It is not splittable since XML has an opening tag at the beginning and a closing tag at the end. You cannot start processing at any point in the middle of those tags.
    * It increases in data size and processing time because the document size is often bulky and with big files. The tag structure makes it huge and complex to read which occurs slow process in parsing, leading also to slower data transmission
    * XML is difficult to be parsed due to its escape character which might be in different form (" " ' ' < < > > & &). The alternative used case is often HTML which only has a simple character (< < > >).
     
* __Ecosystems__
    * XML is practically no used in any enterprise cause it bring many disadvantage in term of storage or exchange
    
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
    
  [source](https://www.alsacreations.com/article/lire/609-XML-en-quelques-mots.html)
       

### 4. AVRO

Avro is a row-oriented remote procedure call and data serialization framework developed within Apache's Hadoop project. It stored its schema in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compacted easily. It attach metadata into their data in each record. 

* __Advantage__
    * Avro is a data serialization system
    * It is splittable (AVRO has a sync marker to separate the block) and compressible
    * Avro is good file format for data exchange and has a data storage which is very compact, fast and efficient for analytics
    * It highly support schema evolution (at different time and independently)
    * It supports batch and mainly a streaming engine
* __Drawback__
    * Its data is not readable by human
    * Avro has a limited environment since all languages don't occur a encoded or decoded.
* __Ecosystems__ 
    * It widely used in many application (Kafka, Spark, ... )
    * Avro provides a remote procedure call (RPC)
    
   ![e.g. AVRO file](https://user-images.githubusercontent.com/51121757/80033375-8232d200-84e4-11ea-9531-076f72e30bea.JPG)

[Source](https://blog.clairvoyantsoft.com/big-data-file-formats-3fb659903271)
    
### 5. Protocol Buffer

Protocol buffer is language-neutral, an extensible way of serializing structured data for use in communications protocols, data storage, and more. It is easy to read and understandably by human. The schema is needed to generate code and read the data. 
   
* __Advantage__
    * Its data is fully typed
    * Protobuf supports schema evolution and batch/streaming processing
    * It is used to serialize data as AVRO
    * It validates the structure. Having a predefined and larger set of data types, messages serialized on Protobuf can be automatically validated by the code that is responsible to exchange them.
* __Drawback__
    * Protobuf is not splittable and not compressible
    * It doesn't support Map reduce
    * It needed a reference file to be decoded 
    * Protocol Buffers are not designed to handle large messages. Since it doesn't support random access. You'll have to read the whole file, even if you only want to access a specific item.
* __Ecosystems__ 
    * It provides tools to generate code for the most used programming languages around, like JavaScript, Java, PHP, C#, Ruby, Objective C, Python, C++ and Go
    * Protobuf is a format support of gRPC in kubernetes [info](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/). RPC is indeed is a network protocol for making procedure calls on a remote computer using an application server.
    * ProfaneDB is a database for Protocol Buffer objects.
  
~~~{r}
      
      message Person {
      required string name = 1;
      required int32 id = 2;
      optional string email = 3;
      }                          
~~~

### 6. Parquet

Parquet is an open source file format for Hadoop ecosystem. Its design is a combined effort between Twitter and Cloudera for an efficient data storage for analytics. Parquet store data by column-oriented like ORC format. It provides efficient data compression and encoding schemes with enhanced performance to handle complex data in bulk. Parquet is a binary file containing metadata about their content. The column metadata is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM). 

 * __Advantage__
     * Parquet is splittable 
     * Organizing by column, It allows a better compression, as data is more homogeneous.
     * It is very efficient for OLAP query.
     * It supports schema evolution and batch/streaming processing
 * __Drawback__
     * Parquet is not human readable
     * It difficult to make it update unless you delete and recreate it again.
 * __Ecosystems__
     * It gives a efficient analysis in BI (Business Intelligence)
     * Parquet is very fast to read in Spark environment
     * Aside Spark and Impala; Arrow, Drill are part of the most compatible platforms of parquet.
     
     
   ![parquet](https://user-images.githubusercontent.com/51121757/80372035-c3333980-888a-11ea-87af-97425e00c476.JPG)

[Source](https://blog.ippon.fr/2020/03/02/de-limportance-du-format-de-la-donnee-pratique-partie-2-2/) 

### 7. ORC (Optimized Row Columnar)

In February 2013, ORC format was announced by Hortonworks in collaboration with Facebook. It is a column-oriented data storage format from the Apache Hadoop ecosystem.
ORC file contains groups of row data called stripes, along with auxiliary information in a file footer. At the end of the file a postscript holds compression parameters and the size of the compressed footer. The default stripe size is 250 MB. Large stripe sizes enable large efficient reads from HDFS.

  * __Advantage__
      * ORC is compressible
      * It reduces the size of the original data up to 75%
      * ORC is more efficient for OLAP than OLTP queries
      * It supports batch/streaming processing 
  * __Drawback__
      * ORC can’t be load data directly into ORCFILE
      * It does not support schema evolution
   * __Ecosystems__ 
      * It brings an efficient analysis in case of Business Intelligence
      * ORC improves performance reading, writing, and processing in Hive
      * Impala currently does not support ORC format
      

   ![ORC](https://user-images.githubusercontent.com/51121757/80372034-c29aa300-888a-11ea-9b37-0114b5a0c0c2.JPG)

[Source](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) 

## [**Conclusion**]()

In general, reading or writing fast according to the type of storage is very related to the kind of query tied to the property of each file format. Usually, row-oriented databases are well-suited for OLTP-like workloads whereas column-oriented systems is well suited of OLAP query. Column data in column based storage is of uniform type; therefore, it will be more easy and better to compress compared a row based storage. 

In term of comparison, JSON, XML are less used in data processing regarding to their lack of splittable. JSON line is however the most suitable to be process in parallel. Generally, Json is the  format file of web applications, serialization and is well known to support schema evolution as AVRO.
In addition to the buffer protocol, AVRO  is considered the privilege format of streaming processing due to fact his ability to support schema evolution. It is also splittable and compressible which occurs it simultaneously the file format appropriate in big data ecosystem. That's what protocol buffer lack about splittable and compressible. However, being a language fully typed as JSON , it bring better performance for compiler optimization and heavily used in kubernetes in case of CRI and gPRC.

The last two formats (ORC and parquet) are optimized the cost storage by avoiding repeated data. They are efficients for Business Intelligence and compatibles with Impala and Hive ecosystems.

In summary, it is important to bear in mind that all these differences between theses files format depend really of the use cases. Some of them could be used by fitting in order to respond a specific purpose.
  
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
