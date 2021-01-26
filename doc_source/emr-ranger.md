# Integrate Amazon EMR with Apache Ranger<a name="emr-ranger"></a>

Beginning with Amazon EMR 5\.32\.0, you can launch a cluster that natively integrates with Apache Ranger\. Apache Ranger is an open\-source framework to enable, monitor, and manage comprehensive data security across the Hadoop platform\. For more information, see [Apache Ranger](https://ranger.apache.org/)\. With native integration, you can bring your own Apache Ranger to enforce fine\-grained data access control on Amazon EMR\.

This section provides a conceptual overview of Amazon EMR integration with Apache Ranger\. It also includes the prerequisites and steps required to launch an Amazon EMR cluster integrated with Apache Ranger\.

Natively integrating Amazon EMR with Apache Ranger provides the following key benefits: 
+ Fine\-grained access control to Hive Metastore databases and tables, which enables you to define data filtering policies at the level of database, table, and column for Apache Spark and Apache Hive applications\. Row\-level filtering and data masking are supported with Hive applications\.
+ The ability to use your existing Hive policies directly with Amazon EMR for Hive applications\.
+ Access control to Amazon S3 data at the prefix and object level, which enables you to define data filtering policies for access to S3 data using the EMR File System\.
+ The ability to use CloudWatch Logs for centralized auditing\.
+ Amazon EMR installs and manages the Apache Ranger plugins on your behalf\.