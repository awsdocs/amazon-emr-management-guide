# Work with Storage and File Systems<a name="emr-plan-file-systems"></a>

Amazon EMR and Hadoop provide a variety of file systems that you can use when processing cluster steps\. You specify which file system to use by the prefix of the URI used to access the data\. For example, `s3://myawsbucket/path` references an Amazon S3 bucket using EMRFS\. The following table lists the available file systems, with recommendations about when it's best to use each one\. 

Amazon EMR and Hadoop typically use two or more of the following file systems when processing a cluster\. HDFS and EMRFS are the two main file systems used with Amazon EMR\.

**Important**  
Beginning with Amazon EMR release version 5\.22\.0, Amazon EMR uses AWS Signature Version 4 exclusively to authenticate requests to Amazon S3\. Earlier Amazon EMR release versions use AWS Signature Version 2 in some cases, unless the release notes indicate that Signature Version 4 is used exclusively\. For more information, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) and [Authenticating Requests \(AWS Signature Version 2\)](https://docs.aws.amazon.com/AmazonS3/latest/API/auth-request-sig-v2.html) in the *Amazon Simple Storage Service Developer Guide*\.


| File System | Prefix | Description | 
| --- | --- | --- | 
| HDFS | hdfs:// \(or no prefix\) |  HDFS is a distributed, scalable, and portable file system for Hadoop\. An advantage of HDFS is data awareness between the Hadoop cluster nodes managing the clusters and the Hadoop cluster nodes managing the individual steps\. For more information, see [Hadoop documentation](http://hadoop.apache.org/docs/stable)\.  HDFS is used by the master and core nodes\. One advantage is that it's fast; a disadvantage is that it's ephemeral storage which is reclaimed when the cluster ends\. It's best used for caching the results produced by intermediate job\-flow steps\.   | 
| EMRFS | s3:// |  EMRFS is an implementation of the Hadoop file system used for reading and writing regular files from Amazon EMR directly to Amazon S3\. EMRFS provides the convenience of storing persistent data in Amazon S3 for use with Hadoop while also providing features like Amazon S3 server\-side encryption, read\-after\-write consistency, and list consistency\.   Previously, Amazon EMR used the S3 Native FileSystem with the URI scheme, `s3n`\. While this still works, we recommend that you use the `s3` URI scheme for the best performance, security, and reliability\.   | 
| local file system |  |  The local file system refers to a locally connected disk\. When a Hadoop cluster is created, each node is created from an EC2 instance that comes with a preconfigured block of preattached disk storage called an *instance store*\. Data on instance store volumes persists only during the life of its EC2 instance\. Instance store volumes are ideal for storing temporary data that is continually changing, such as buffers, caches, scratch data, and other temporary content\. For more information, see [Amazon EC2 Instance Storage](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)\.  | 
| \(Legacy\) Amazon S3 block file system | s3bfs:// |  The Amazon S3 block file system is a legacy file storage system\. We strongly discourage the use of this system\.  We recommend that you do not use this file system because it can trigger a race condition that might cause your cluster to fail\. However, it might be required by legacy applications\.   | 

**Note**  
The `s3a` protocol is not supported\. We suggest you use `s3` in place of `s3a`\.

## Access File Systems<a name="emr-dev-access-file-systems"></a>

You specify which file system to use by the prefix of the uniform resource identifier \(URI\) used to access the data\. The following procedures illustrate how to reference several different types of file systems\. 

**To access a local HDFS**
+ Specify the `hdfs:///` prefix in the URI\. Amazon EMR resolves paths that do not specify a prefix in the URI to the local HDFS\. For example, both of the following URIs would resolve to the same location in HDFS\. 

  ```
  1. hdfs:///path-to-data
  2. 							
  3. /path-to-data
  ```

**To access a remote HDFS**
+ Include the IP address of the master node in the URI, as shown in the following examples\. 

  ```
  1. hdfs://master-ip-address/path-to-data
  2. 						
  3. master-ip-address/path-to-data
  ```

**To access Amazon S3**
+ Use the `s3://` prefix\.

  ```
  1. 						
  2. s3://bucket-name/path-to-file-in-bucket
  ```

**To access the Amazon S3 block file system**
+ Use only for legacy applications that require the Amazon S3 block file system\. To access or store data with this file system, use the `s3bfs://` prefix in the URI\. 

  The Amazon S3 block file system is a legacy file system that was used to support uploads to Amazon S3 that were larger than 5 GB in size\. With the multipart upload functionality Amazon EMR provides through the AWS Java SDK, you can upload files of up to 5 TB in size to the Amazon S3 native file system, and the Amazon S3 block file system is deprecated\. 
**Warning**  
Because this legacy file system can create race conditions that can corrupt the file system, you should avoid this format and use EMRFS instead\. 

  ```
  1. s3bfs://bucket-name/path-to-file-in-bucket
  ```