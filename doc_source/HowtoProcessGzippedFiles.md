# How to Process Compressed Files<a name="HowtoProcessGzippedFiles"></a>

Hadoop checks the file extension to detect compressed files\. The compression types supported by Hadoop are: gzip, bzip2, and LZO\. You do not need to take any additional action to extract files using these types of compression; Hadoop handles it for you\.

To index LZO files, you can use the hadoop\-lzo library which can be downloaded from [https://github\.com/kevinweil/hadoop\-lzo](https://github.com/kevinweil/hadoop-lzo)\. Note that because this is a third\-party library, Amazon EMR does not offer developer support on how to use this tool\. For usage information, see [the hadoop\-lzo readme file\.](https://github.com/kevinweil/hadoop-lzo/blob/master/README.md) 