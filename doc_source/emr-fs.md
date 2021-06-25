# Use EMR File System \(EMRFS\)<a name="emr-fs"></a>

The EMR File System \(EMRFS\) is an implementation of HDFS that all Amazon EMR clusters use for reading and writing regular files from Amazon EMR directly to Amazon S3\. EMRFS provides the convenience of storing persistent data in Amazon S3 for use with Hadoop while also providing features like consistent view and data encryption\. 

Consistent view provides consistency checking for list and read\-after\-write \(for new put requests\) for objects in Amazon S3\. Data encryption allows you to encrypt objects that EMRFS writes to Amazon S3, and enables EMRFS to work with encrypted objects in Amazon S3\. If you are using Amazon EMR release version 4\.8\.0 or later, you can use security configurations to set up encryption for EMRFS objects in Amazon S3, along with other encryption settings\. For more information, see [Encryption options](emr-data-encryption-options.md)\. If you use an earlier release version of Amazon EMR, you can manually configure encryption settings\. For more information, see [Specifying Amazon S3 encryption using EMRFS properties](emr-emrfs-encryption.md)\.

When using Amazon EMR release version 5\.10\.0 or later, you can use different IAM roles for EMRFS requests to Amazon S3 based on cluster users, groups, or the location of EMRFS data in Amazon S3\. For more information, see [Configure IAM roles for EMRFS requests to Amazon S3](emr-emrfs-iam-roles.md)\.

**Warning**  
Before turning on speculative execution for Amazon EMR clusters running Apache Spark jobs, please review the following information\.  
EMRFS includes the EMRFS S3\-optimized committer, an OutputCommitter implementation that is optimized for writing files to Amazon S3 when using EMRFS\. If you turn on the Apache Spark speculative execution feature with applications that write data to Amazon S3 and do not use the EMRFS S3\-optimized committer, you may encounter data correctness issues described in [SPARK\-10063](https://issues.apache.org/jira/browse/SPARK-10063)\. This can occur if you are using Amazon EMR versions earlier than EMR release 5\.19, or if you are writing files to Amazon S3 using formats such as ORC and CSV, which are not supported by the EMRFS S3\-optimized committer\. For a complete list of requirements for using the EMRFS S3\-optimized committer, see [Requirements for the EMRFS S3\-optimized committer](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-committer-reqs.html)\.  
EMRFS direct write is typically used when the EMRFS S3\-optimized committer is not supported, such as when writing the following:  
An output format other than Parquet, such as ORC or text\.
Hadoop files using the Spark RDD API\.
Parquet using Hive SerDe\. See [Hive metastore Parquet table conversion](https://spark.apache.org/docs/latest/sql-data-sources-parquet.html#hive-metastore-parquet-table-conversion)\.
EMRFS direct write is not used in the following scenarios:  
When the EMRFS S3\-optimized committer is enabled\. See [Requirements for the EMRFS S3\-optimized committer](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-committer-reqs.html)\.
When writing dynamic partitions with partitionOverwriteMode set to dynamic\.
When writing to custom partition locations, such as locations that do not conform to the Hive default partition location convention\.
When using file systems other than EMRFS, such as writing to HDFS or using the S3A file system\.
To determine whether your application uses direct write in Amazon EMR 5\.14\.0 or later, enable Spark INFO logging\. If a log line containing the text "Direct Write: ENABLED" is present in either Spark driver logs or Spark executor container logs, then your Spark application wrote using direct write\.  
By default, speculative execution is turned `OFF` on Amazon EMRclusters\. We highly recommend that you do not turn speculative execution on if both of these conditons are true:  
You are writing data to Amazon S3\.
Data is written in a format other than Apache Parquet or in Apache Parquet format not using the EMRFS S3\-optimized committer\.
If you turn on Spark speculative execution and write data to Amazon S3 using EMRFS direct write, you may experience intermittent data loss\. When you write data to HDFS, or write data in Parquet using the EMRFS S3\-optimized committer, Amazon EMR does not use direct write and this issue does not occur\.  
If you need to write data in formats that use EMRFS direct write from Spark to Amazon S3 and use speculative execution, we recommend writing to HDFS and then transferring output files to Amazon S3 using S3DistCP\.

**Topics**
+ [Consistent view](emr-plan-consistent-view.md)
+ [Authorizing access to EMRFS data in Amazon S3](emr-plan-credentialsprovider.md)
+ [Managing the default AWS Security Token Service endpoint](emr-emrfs-sts-endpoint.md)
+ [Specifying Amazon S3 encryption using EMRFS properties](emr-emrfs-encryption.md)