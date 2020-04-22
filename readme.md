The Goal is to understand what files between them are suitable:

  Get read fast (mainly the case of row based storage)

  Get written fast (mainly the case row based storage)

  Executed quickly a OLAP or OLTP query 

  Be splittable i.e. multiple task can run parallel on parts of file

 Support Schema evolution, allowing us to change schema of file

 Support advanced compression through various available compression codecs (Bzip2, LZO, Sappy). That allow to reduce data storage space on disk, increase the performance of lecture and readable on the disc and also the transfer speed in the network.


**Row based file format**
------
## CSV
It’s commonly used to exchange tabular data between systems using plan text, splittable , less compressible file.
Advantage : human-readable and easy to edit manually; provide a straightforward information schema;  processed by almost all existing applications; simple to implement and parse, write performance but slow to read
Drawback : don’t manage Null value and not standard for Big data

