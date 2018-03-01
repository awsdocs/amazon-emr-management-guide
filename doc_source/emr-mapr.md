# Using the MapR Distribution for Hadoop<a name="emr-mapr"></a>

MapR is a third\-party application offering an open, enterprise\-grade distribution that makes Hadoop easier to use and more dependable\. For ease of use, MapR provides network file system \(NFS\) and open database connectivity \(ODBC\) interfaces, a comprehensive management suite, and automatic compression\. For dependability, MapR provides high availability with a self\-healing no\-NameNode architecture, and data protection with snapshots, disaster recovery, and with cross\-cluster mirroring\. For more information about MapR, go to [http://www\.mapr\.com/](http://www.mapr.com/)\. 

 There are several editions of MapR available on Amazon EMR: 

+ **M3 Edition** \(versions 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2, 2\.1\.3\)—The free version of a complete distribution for Hadoop\. M3 delivers a fully random, read\-write–capable platform that supports industry\-standard interfaces \(e\.g\., NFS, ODBC\), and provides management, compression, and performance advantages\. 

+ **M5 Edition** \(versions 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2, 2\.1\.3\)—The complete distribution for Apache Hadoop that delivers enterprise\-grade features for all file operations on Hadoop\. M5 features include mirroring, snapshots, NFS HA, and data placement control\. For more information, including pricing, see [MapR on Amazon EMR Detail Page\.](https://aws.amazon.com//elasticmapreduce/mapr) 

+ **M7 Edition** \(versions 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2\)—The complete distribution for Apache Hadoop that delivers ease of use, dependability, and performance advantages for NoSQL and Hadoop applications\. M7 provides scale, strong consistency, reliability, and continuous low latency with an architecture that does not require compactions or background consistency checks\. For more information, including pricing, see [MapR on Amazon EMR Detail Page\.](https://aws.amazon.com//elasticmapreduce/mapr) 

**Note**  
For enterprise\-grade reliability and consistent performance for Apache HBase applications, use the MapR M7 edition\.  
In addition, MapR does not support Ganglia and debugging and the MapR M3 and M5 editions on Amazon EMR do not support Apache HBase\.  
The version of MapR available in Amazon EMR that supports Hadoop 2\.x is 4\.0\.2, and it is only available with Amazon EMR AMI 3\.3\.2