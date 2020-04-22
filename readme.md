The Goal is to understand what files between them are suitable:

	Get read fast (mainly the case of row based storage)

  Get written fast (mainly the case row based storage)

  Executed quickly a OLAP or OLTP query 

  Be splittable i.e. multiple task can run parallel on parts of file

 Support Schema evolution, allowing us to change schema of file

 Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy). That allow to reduce data storage space on disk, increase the performance of lecture and readable on the disc and also the transfer speed in the network.


**Row based file format**
------
