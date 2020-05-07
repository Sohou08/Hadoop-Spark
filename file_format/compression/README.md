
# COMPRESSION DATA #

Compressing big data help by reducing the amount of storage and bandwidth required for data sets, remove irrelevant or redundant data, making analysis and processing easier and faster.
Generally, the codec term is employed for describing the type of compression schemes which is the shortened form of compressor/decompressor. There exist many kinds of compression type involving various proprieties of splittable, in terms of performance and ration of compression.

Let's describe first those proprieties 


+	__Splittable compression__

This one is very important is Hadoop context. Indeed, the way Hadoop works is that files are split if they’re larger than the file’s block size setting, and individual file splits can be processed in parallel by different mappers.

With most codecs, text file splits cannot be decompressed independently of other splits from the same file, so those codecs are said to be non-splittable, so MapReduce processing is limited to a single mapper. 

Splittable compression is only a factor for text files. For binary files, Hadoop compression codecs compress data within a binary-encoded container, depending on the file type (for example, a SequenceFile, Avro, or ProtocolBuffer).

+	__Performance__

Speaking performance could be explained in case of input file to a MapReduce. If this one contains compressed data, the time that is needed to read that data from HDFS is reduced and job performance is enhanced. The input data is decompressed automatically when it is being read by MapReduce.

Compressing the output can result in significant performance improvements. Because map function output is written to disk and shipped across the network to the reduce tasks.

+	__Compression Ratio (degree to which a file is compressed) and Compress/decompress speeds__

Compression ratio is a measurement of the relative reduction in size of data representation produced by a data compression algorithm.
The speed is more based of the performance time to compress or decompress a given data file. 


## 1. Gzip (short for GNU zip)

 Gzip generates compressed files that have a .gz extension. Gunzip command can be used to decompress files that were created by a number of compression utilities, including Gzip.
   * __Advantage__
      * 

## 2. Bzip2

From a usability standpoint, Bzip2 and Gzip are similar. 

* __Advantage__
   * If you’re setting up an archive that you’ll rarely need to query and space is at a high premium, then maybe would Bzip2 be worth considering.
   * It generates a better compression ratio than does Gzip
   * splittable
* __Drawback__
   * all the available compression codecs in Hadoop, Bzip2 is by far the slowest.
   
## 3. Snappy

The Snappy codec from Google provides modest compression ratios, but fast compression and decompression speeds. (In fact, it has the fastest decompression speeds, which makes it highly desirable for data sets that are likely to be queried often.)

You can use Snappy as an add-on for more recent versions of Hadoop that do not yet provide Snappy codec support.

* __Advantage__
   * default compression parquet in Impala
   * combination of fast compression and decompression 
* __Drawback__
   * 
   

## 4. LZO (Lempel-Ziv-Oberhumer)

Similar to Snappy, LZO provides modest compression ratios, but fast compression and decompression speeds. 

LZO supports splittable compression, which enables the parallel processing of compressed text file splits by your MapReduce jobs. LZO needs to create an index when it compresses a file, because with variable-length compression blocks, an index is required to tell the mapper where it can safely split the compressed file. LZO is mainly really desirable if you need to compress text files.

## 4. Zstd
Zstd is a real-time compression algorithm offering a tradeoff between speed and ratio of compression. Compression levels from 1 up to 22 are supported. The lower the level, the faster the speed at the cost of compression ratio.
