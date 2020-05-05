
# BIG DATA FILE FORMATS: BENCHMARK STUDY BETWEEN CSV, ORC, JSON, PROTOCOLE BUFFER, AVRO, PARQUET, XML #

Big data ecosystem (Hadoop, Apache) support multiples types of files formats according of the analysis purpose. Thus, the goal of this report is to understand what are the most suitable to:

+	__Row-based storage and column-based storage__

The choice of a big data format need to known whether a row or column-based format is best suited to your objectives. Column-based storage (ORC, Parquet) is most useful when performing analytics queries that require only a subset of columns examined over very large data sets. If your queries require access to all or most of the columns of each row of data, row-based storage (CSV,JSON, AVRO,...) will be better suited to your needs. 


+	__Perform OLAP or OLTP query__ 

OLTP is an online Transaction Processing system. The main focus of OLTP system is to record the current Update, Insertion and Deletion while transaction. OLAP is online Analytical Processing system. His main operation is to extract multidimensional data for analysis. 

+	__Be splittable i.e. multiple task can run parallel on parts of file__

Hadoop stores and processes data in blocks. So, be able to begin reading data at any point within a file is helpful in order to take fullest advantage of Hadoop’s distributed processing. Thereupon, splittable files is suitable so that its massively distributed engine can leverage data locality and parallel processing. That the case of JSON and XML which doesn't take advantage of that because they are not splittable.

+   __Support advanced compression through various compression codecs (Bzip2, LZO, Sappy,...)__.

Data compression is useful approach for storing the large volume of structured data in many applications.
It allows to reduce data storage space on disk; increase the performance of lecture and readable on the disc ; also improve the speed of file transfer over the network. There are many kind of compression algorithm which differ according of the ratio of compression, speed, performance, splittable,... [For more information about compression](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/compression)

+  __Streaming and Batch__

Batch processing is where the processing happens of blocks of data that have already been stored over a period of time. For example, processing all the transaction that have been performed by a major financial firm in a week. 
Stream processing allows to process data in real time as they arrive and quickly detect conditions within small time period from the point of receiving the data (e.g.fraud detection). In the second case, AVRO is considered the best format about that explained by his highly schema evolution. 

+	__Support Schema evolution, allowing us to change schema of file__

The schema will store the definition of each attribute and its type. Unless your data is guaranteed to never change, you’ll need to think about schema evolution in order to figure out if your data schema changes over time, does it possible to manage fields as added, deleted or renamed. Apart from AVRO, there are other file formats which support this property as protocol buffer, JSON, parquet,...

According of those properties , let's describe briefly each file formats and their characteristics.

## **Row-based file format**

### 1. CSV and TSV

CSV and TSV are a text Input Format. Both are splittable and compressible and use newline as the record delimiter. They are human-readable and easy to edit manually, processed by almost all existing applications.
In terms of comparison:

__CSV__ : it represents a lines of comma separated fields with an optional header. It uses an escape syntax to represents comma and a new line

  Here, using escapes to represent commas and quotes in data
  
~~~ {r}
  "Field1","Field2","Field3"
  "pap","affected, where!","date"
  "mam","No""affected, where!""","day"
~~~ 

The same data in TSV will be:
~~~ {r}
Field1	Field2	Field3
pap affected, where!	date
mam No "affected, where!"	day
~~~
  
   * __Advantage__
       * splittable and compressible
       * usually batch processed
   * __Drawback__
       * doesn't support null value 
       * Reading require programs to sparse the escape syntax
       * No schema evolution
       * doesn't support streaming unless you define manually in advance the schema 
       * No standard for Big data
   * __Big data__ 
       * Support a wide range of applications


__TSV__: it use TAB as default field delimiter
  
   * __Advantage__
       * splittable and compressible
       * usually batch processed
       * More easy to parse than CSV mainly due to the lack of escape syntax
   * __Drawback__
       * doesn't support null values and schema evolution
       * as CSV doesn't support streaming unless you define manually in advance the schema 
       * No standard for Big data
   * __Big data__ 
       *  Support a wide range of applications
 
  [source](https://github.com/eBay/tsv-utils/blob/master/README.md)

### 2. JSON (JavaScript object notation) 

JSON is a text Input Format containing record which might be in any form (string, integer, booleans, array, object, key-value, nested data...). It support serialization and deserialization process. 
 
* __Advantage__
    * compressible 
    * data exchange format
    * Support batch/streaming processing
    * store metadata with data and support schema evolution
    * fully typed performing the compiler optimization.
    * lightweight text-based format in comparison to XML 
* __Drawback__
    * No splittable 
    * Lack indexing 
* __Big data__ 
    *  widely used for NoSQL databases as MongoDB, due to the fact they are easier to modify there than in structured database as Postgres where you'd need to extract the whole document for changing.
    * GSON library is considered very efficient for parsing JSON file
    
~~~ {r}
       {
          "StudentId": "trgfg-5454-fdfdg-4346",
          "Grade": 7,
          "StudentClassCollection": [
                {
                    "ClassId": "89084343",
                    "ClassParticipation": "Satisfied",
                    "ClassParticipationRank": "High",
                    "Score": 93,
                    "PerformedActivity": false
                },
                {
                    "ClassId": "78547522",
                    "ClassParticipation": "NotSatisfied",
                    "ClassParticipationRank": "None",
                    "Score": 74,
                    "PerformedActivity": false
                },
                        ]
        }
~~~ 

### 3. XML 

XML is a input text format. As Json,  It is generally used to serialize, encapsulate, and exchange data. 
XML syntax is verbose, especially for human readers, relative to other alternatives ‘text-based’ formats. 

* __Advantage__
    * data exchange format
    * Support batch/streaming processing
    * store meta data with data and support schema evolution
* __Drawback__
    * No splittable : XML has an opening tag at the beginning and a closing tag at the end. You cannot start processing at any point in the middle of those tags.
    * increase in data size and processing time because the document size is often bulky and with big files, the tag structure makes it huge and complex to read which occurs slow process in parsing, leading also to slower data transmission
* __Big data__ 
    * Pig (To process XMLs in Pig, piggybank.jar is essential. This jar contains a UDF called XMLLoader() that will be used to read the XML document),..
       
       ~~~ {r}
       <?xml version="1.0"?>
       <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

       <!-- Do not modify this file directly.  Instead, copy entries that you -->
       <!-- wish to modify from this file into hdfs-site.xml and change them -->
       <!-- there.  If hdfs-site.xml does not already exist, create it.      -->

       <configuration>

       <property>
         <name>dfs.namenode.logging.level</name>
         <value>info</value>
         <description>The logging level for dfs namenode. Other values are "dir"(trac
       e namespace mutations), "block"(trace block under/over replications and block
       creations/deletions), or "all".</description>
       </property>
       ~~~ 
    
  [source](https://github.com/facebookarchive/hadoop-20/blob/master/src/hdfs/hdfs-default.xml)
       

### 4. AVRO

AVRO stored his schema in JSON format while the data is stored in binary format, minimizing file size and maximizing efficiency and to be compacted easily. It attach metadata into their data in each record. 

* __Advantage__
    * Splittable (AVRO has a sync marker to separate the block) 
    * data storage is very compact, fast and efficient 
    * Compressible (at different time and independently)
    * Highly support schema evolution
    * Support batch and mostly streaming processing
    * Avro serializes fast and the data resulting after serialization is least in size with schemas.
* __Drawback__
    
* __Big data__ 
    * widely used in many application
    * Impala cannot write Avro data files
       

   ![e.g. AVRO file](https://user-images.githubusercontent.com/51121757/80033375-8232d200-84e4-11ea-9531-076f72e30bea.JPG)

[Source](https://blog.clairvoyantsoft.com/big-data-file-formats-3fb659903271)
    
### 5. Protocol Buffer

Protocol buffer is language-neutral, an extensible way of serializing structured data for use in communications protocols, data storage, and more. You can easily read it and understand it as an human. The schema is needed to generate code and read the data. 
   
* __Advantage__
    * data fully typed
    * support schema evolution
    * Support batch/streaming
    * As avro , it mainly used to serialize data
* __Drawback__
    * No splittable and No Compressible
    * doesn't support Map reduce
* __Big data__ 
    * widely used in many application
    * ProfaneDB is a database for Protocol Buffer objects.
    
~~~{r}
message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phone = 4;
}
~~~
   
[Source](https://developers.google.com/protocol-buffers/docs/overview)  

## **Column-based file format**

### 6. Parquet

Parquet is a binary file containing  metadata about their content. The column metadata for a Parquet file is stored at the end of the file, which allows for fast, one-pass writing. Parquet is optimized for the write Once read many (WORM). 

 * __Advantage__
     * splittable and compressible by default
     * organizing by column allows for better compression, as data is more homogeneous.
     * efficient for OLAP query
     * support schema evolution
     * Support batch/streaming processing
 * __Drawback__
     * difficult to make a update in parquet table unless you delete and recreate again the given subset.
 * __Big data__ 
     * very fast to read in Spark environment
     * Hive and Impala are able to query newly added columns, but other tools in the ecosystem such as Hadoop Pig may face challenges.
     
     
   ![parquet](https://user-images.githubusercontent.com/51121757/80372035-c3333980-888a-11ea-87af-97425e00c476.JPG)

[Source](https://blog.ippon.fr/2020/03/02/de-limportance-du-format-de-la-donnee-pratique-partie-2-2/) 

### 7. ORC

ORC file contains groups of row data called stripes, along with auxiliary information in a file footer. At the end of the file a postscript holds compression parameters and the size of the compressed footer.
The default stripe size is 250 MB. Large stripe sizes enable large, efficient reads from HDFS.

  * __Advantage__
      * compressible
      * reduce the size of the original data up to 75%
      * OLAP (more efficient)/OLTP
      * Support batch
   * __Drawback__
      * can’t be load data directly into ORCFILE
      * does not support schema evolution
   * __Big data__ 
      * improves performance reading, writing, and processing in Hive
      

   ![ORC](https://user-images.githubusercontent.com/51121757/80372034-c29aa300-888a-11ea-9b37-0114b5a0c0c2.JPG)

[Source](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) 

## __Conclusion__

In general, reading or writing fast according to the type of storage is very related to the kind of query tied to the property of each file format. Usually, column-based storage lets you ignore all the data that doesn’t apply to a particular query, because you can retrieve the information from just the columns you want. By contrast, if you were working with a row-oriented storage and you wanted to know, the average population density in cities with more than a million people, your query would access each record in the table (meaning all of its fields) to get the information from the two columns whose data you needed.That would involve a lot of unnecessary disk seeks and disk reads, which also impact performance. 

Even all file formats could be used for each type of query, the row-based storage will be more adapted to OLTP query inversely for columns based storage which is adapted of OLAP query. 

In term of comparison, JSON, XML are less used in data processing regarding to their lack of splittable. Most of case , they are used to exchange data over network and serialization and well known to support schema evolution as AVRO. This one is considered actually the leader format in case of streaming processing due to fact his ability to support schema evolution. In addition, it is splittable and compressible which occurs it simultaneously the file format appropriate in big data ecosystem. That what protocol buffer lack about splittable, compressible. However, being a language fully typed as JSON , it bring better performance for compiler optimization.

The last two formats (ORC and parquet) are optimized the cost storage by avoiding repeated data. They are used in many ecosystems of big data but strongly in hive.

In general, all these differences are not statics. It's can evolve and differ according to the use cases. Some of them could be fit in order to respond a specific purpose.
  
##  Overview

[Comparison table 1](https://user-images.githubusercontent.com/51121757/80630250-d6413780-8a4b-11ea-912c-6a31a5b9da0b.JPG)

[Comparison table 2](https://user-images.githubusercontent.com/51121757/80630241-d5100a80-8a4b-11ea-87bf-657ef500183d.JPG)


[e.g. File format comparison](https://2s7gjr373w3x22jf92z99mgm5w-wpengine.netdna-ssl.com/wp-content/uploads/2018/05/Nexla-File-Format.png), [e.g. Comparison in terms of reading, space,storage, execution time,...](https://luminousmen.com/post/big-data-file-formats)


[Let's test all these differences practically](https://github.com/Sohou08/Hadoop-Spark/tree/master/file_format/pratice_test)...
