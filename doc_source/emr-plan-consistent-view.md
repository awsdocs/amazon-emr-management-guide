# Consistent view<a name="emr-plan-consistent-view"></a>

**Important**  
You no longer need to use EMRFS Consistent View as Amazon S3 supports strong read\-after\-write Consistency\. See [Strong read\-after\-write consistency](https://aws.amazon.com/blogs/aws/amazon-s3-update-strong-read-after-write-consistency/)\. This works with all Amazon EMR versions\.

EMRFS consistent view is an optional feature available when using Amazon EMR release version 3\.2\.1 or later\. Consistent view allows EMR clusters to check for list and read\-after\-write consistency for Amazon S3 objects written by or synced with EMRFS\. Consistent view addresses an issue that can arise due to the [Amazon S3 data consistency model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel)\. For example, if you add objects to Amazon S3 in one operation and then immediately list objects in a subsequent operation, the list and the set of objects processed may be incomplete\. This is more commonly a problem for clusters that run quick, sequential steps using Amazon S3 as a data store, such as multi\-step extract\-transform\-load \(ETL\) data processing pipelines\.

When you create a cluster with consistent view enabled, Amazon EMR uses an Amazon DynamoDB database to store object metadata and track consistency with Amazon S3\. You must grant EMRFS role with permissions to access DynamoDB\. If consistent view determines that Amazon S3 is inconsistent during a file system operation, it retries that operation according to rules that you can define\. By default, the DynamoDB database has 400 read capacity and 100 write capacity\. You can configure read/write capacity settings depending on the number of objects that EMRFS tracks and the number of nodes concurrently using the metadata\. You can also configure other database and operational parameters\. Using consistent view incurs DynamoDB charges, which are typically small, in addition to the charges for Amazon EMR\. For more information, see [Amazon DynamoDB pricing](https://aws.amazon.com/dynamodb/pricing/)\.

With consistent view enabled, EMRFS returns the set of objects listed in an EMRFS metadata store and those returned directly by Amazon S3 for a given path\. Because Amazon S3 is still the "source of truth" for the objects in a path, EMRFS ensures that everything in a specified Amazon S3 path is being processed regardless of whether it is tracked in the metadata\. However, EMRFS consistent view only ensures that the objects in the folders that you track are checked for consistency\.

You can use the EMRFS utility \(`emrfs`\) from the command line of the master node to perform operations on Amazon S3 objects that are tracked by consistent view\. For example, you can import, delete, and sync Amazon S3 objects with the EMRFS metadata store\. For more information about the EMRFS CLI utility, see [EMRFS CLI Command Reference](emrfs-cli-reference.md)\.

If you directly delete objects from Amazon S3 that are tracked in EMRFS metadata, EMRFS treats the object as inconsistent and throws an exception after it has exhausted retries\. Use EMRFS to delete objects in Amazon S3 that are tracked using consistent view\. Alternatively, you can use the `emrfs` command line to purge metadata entries for objects that have been directly deleted, or you can sync the consistent view with Amazon S3 immediately after you delete the objects\.

**Note**  
 It is recommended to enable Time to Live \(TTL\) for the DynamoDB table created by EMRFS\. The default table name is `EmrFSMetadata`\. TTL can save cost by deleting stored data volumes that are no longer needed\. For more information, see [Enabling time to live \(TTL\)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-how-to.html)\. 

**Topics**
+ [Enable consistent view](enable-consistent-view.md)
+ [Understanding how EMRFS consistent view tracks objects in Amazon S3](emrfs-files-tracked.md)
+ [Retry logic](emrfs-retry-logic.md)
+ [EMRFS consistent view metadata](emrfs-metadata.md)
+ [Configure consistency notifications for CloudWatch and Amazon SQS](emrfs-configure-sqs-cw.md)
+ [Configure consistent view](emrfs-configure-consistent-view.md)
+ [EMRFS CLI Command Reference](emrfs-cli-reference.md)