# How to Get Data Into Amazon EMR<a name="emr-plan-get-data-in"></a>

Amazon EMR provides several ways to get data onto a cluster\. The most common way is to upload the data to Amazon S3 and use the built\-in features of Amazon EMR to load the data onto your cluster\. You can also use the Distributed Cache feature of Hadoop to transfer files from a distributed file system to the local file system\. The implementation of Hive provided by Amazon EMR \(Hive version 0\.7\.1\.1 and later\) includes functionality that you can use to import and export data between DynamoDB and an Amazon EMR cluster\. If you have large amounts of on\-premises data to process, you may find the AWS Direct Connect service useful\. 

**Topics**
+ [Upload Data to Amazon S3](emr-plan-upload-s3.md)
+ [Import files with Distributed Cache](emr-plan-input-distributed-cache.md)
+ [How to Process Compressed Files](HowtoProcessGzippedFiles.md)
+ [Import DynamoDB Data into Hive](emr-plan-input-dynamodb.md)
+ [Connect to Data with AWS DirectConnect](emr-plan-input-directconnect.md)
+ [Upload Large Amounts of Data with AWS Import/Export](emr-plan-input-import-export.md)