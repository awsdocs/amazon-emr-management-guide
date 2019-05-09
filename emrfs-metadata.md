# EMRFS Consistent View Metadata<a name="emrfs-metadata"></a>

EMRFS consistent view tracks consistency using a DynamoDB table to track objects in Amazon S3 that have been synced with or created by EMRFS\. The metadata is used to track all operations \(read, write, update, and copy\), and no actual content is stored in it\. This metadata is used to validate whether the objects or metadata received from Amazon S3 matches what is expected\. This confirmation gives EMRFS the ability to check list consistency and read\-after\-write consistency for new objects EMRFS writes to Amazon S3 or objects synced with EMRFS\. Multiple clusters can share the same metadata\.

**How to add entries to metadata**  
You can use the `sync` or `import` subcommands to add entries to metadata\. `sync` reflects the state of the Amazon S3 objects in a path, while `import` is used strictly to add new entries to the metadata\. For more information, see [EMRFS CLI Reference](emrfs-cli-reference.md)\.

**How to check differences between metadata and objects in Amazon S3**  
To check for differences between the metadata and Amazon S3, use the `diff` subcommand of the EMRFS CLI\. For more information, see [EMRFS CLI Reference](emrfs-cli-reference.md)\.

**How to know if metadata operations are being throttled**  
EMRFS sets default throughput capacity limits on the metadata for its read and write operations at 400 and 100 units, respectively\. Large numbers of objects or buckets may cause operations to exceed this capacity, at which point they are throttled by DynamoDB\. For example, an application may cause EMRFS to throw a `ProvisionedThroughputExceededException` if you are performing an operation that exceeds these capacity limits\. Upon throttling, the EMRFS CLI tool attempts to retry writing to the DynamoDB table using [exponential backoff](https://docs.aws.amazon.com/general/latest/gr/api-retries.html) until the operation finishes or when it reaches the maximum retry value for writing objects from Amazon EMR to Amazon S3\. 

You can also view Amazon CloudWatch metrics for your EMRFS metadata in the DynamoDB console where you can see the number of throttled read and write requests\. If you do have a non\-zero value for throttled requests, your application may potentially benefit from increasing allocated throughput capacity for read or write operations\. You may also realize a performance benefit if you see that your operations are approaching the maximum allocated throughput capacity in reads or writes for an extended period of time\.

**Throughput characteristics for notable EMRFS operations**  
The default for read and write operations is 400 and 100 throughput capacity units, respectively\. The following performance characteristics give you an idea of what throughput is required for certain operations\. These tests were performed using a single\-node `m3.large` cluster\. All operations were single threaded\. Performance differs greatly based on particular application characteristics and it may take experimentation to optimize file system operations\.


| Operation  | Average read\-per\-second  | Average write\-per\-second  | 
| --- | --- | --- | 
| create \(object\) | 26\.79 |  6\.70 | 
| delete \(object\) | 10\.79 |  10\.79 | 
| delete \(directory containing 1000 objects\) | 21\.79 | 338\.40  | 
|  getFileStatus \(object\) | 34\.70 | 0  | 
| getFileStatus \(directory\) | 19\.96 | 0 | 
| listStatus \(directory containing 1 object\) | 43\.31 | 0 | 
| listStatus \(directory containing 10 objects\) | 44\.34 | 0 | 
| listStatus \(directory containing 100 objects\) | 84\.44 | 0 | 
| listStatus \(directory containing 1,000 objects\) | 308\.81 | 0 | 
| listStatus \(directory containing 10,000 objects\) | 416\.05 | 0 | 
| listStatus \(directory containing 100,000 objects\) | 823\.56 | 0 | 
| listStatus \(directory containing 1M objects\) | 882\.36 | 0 | 
| mkdir \(continuous for 120 seconds\)  | 24\.18 | 4\.03 | 
| mkdir | 12\.59 | 0 | 
| rename \(object\) | 19\.53 | 4\.88 | 
| rename \(directory containing 1000 objects\) | 23\.22 | 339\.34 | 

**To submit a step that purges old data from your metadata store**  
Users may wish to remove particular entries in the DynamoDB\-based metadata\. This can help reduce storage costs associated with the table\. Users have the ability to manually or programmatically purge particular entries by using the EMRFS CLI `delete` subcommand\. However, if you delete entries from the metadata, EMRFS no longer makes any checks for consistency\.

Programmatically purging after the completion of a job can be done by submitting a final step to your cluster, which executes a command on the EMRFS CLI\. For instance, type the following command to submit a step to your cluster to delete all entries older than two days\.

```
aws emr add-steps --cluster-id j-2AL4XXXXXX5T9 --steps Name="emrfsCLI",Jar="command-runner.jar",Args=["emrfs","delete","--time","2","--time-unit","days"]
{
    "StepIds": [
        "s-B12345678902"
    ]
}
```

Use the StepId value returned to check the logs for the result of the operation\.