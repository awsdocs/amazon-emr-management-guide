# MapR Editions, Versions, and Arguments<a name="emr-mapr-editions-versions-arguments"></a>

MapR supports several editions, versions, and arguments that you can pass to it using the Amazon EMR console or CLI\. The following examples use the Amazon EMR CLI, however Amazon EMR provides an equivalent for each that you can use with the console\.

For more information about launching clusters using the CLI, see the instructions for each cluster type in [Plan and Configure Clusters](emr-plan.md)\. 

## Editions<a name="emr-mapr-editions"></a>

Using the CLI, you can pass the `--edition` parameter to specify the following editions:

+ m3

+ m5

+ m7

The default edition is **m3** if not specified\.

## Versions<a name="emr-mapr-versions"></a>

You can use `--version` to specify the following versions: 

+ M3 Edition \- 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2, 2\.1\.3

+ M5 Edition \- 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2, 2\.1\.3

+ M7 Edition \- 4\.0\.2, 3\.1\.1, 3\.0\.3, 3\.0\.2

**Note**  
MapR version 4\.0\.2 is available with AMI version 3\.3\.2 only\.

## Arguments<a name="emr-mapr-arguments"></a>

The `--supported-product` option supports optional arguments\. Amazon EMR accepts and forwards the argument list to the corresponding installation script as bootstrap action arguments\. The following example shows the syntax for using the `--supported-product` option with or without optional arguments\. The fields **key1** and **value1**, etc\. represent custom key/value pairs that MapR recognizes and the key named `edition` is shown among them as an example: 

```
--supported-product mapr-m3
--supported-product mapr-m3 --args "--key1,value1,--key2,value2" 
--supported-product mapr --args "--edition,m3,--key1,value1,--key2,value2" 
--supported-product mapr-m3 --args "--edition,m3,--key1,value1,--key2,value2"
```

The following table lists the parameters that MapR reads when users specify them with the `--args` option\.


| Parameter | Description | 
| --- | --- | 
| \-\-edition |  The MapR edition\.  | 
| \-\-version |  The MapR version\.  | 
| \-\-owner\-password |  The password for the Hadoop owner\. The default is `hadoop`\.  | 
| \-\-num\-mapr\-masters |  Any additional master nodes for m5/m7 clusters\.  | 
| \-\-mfs\-percentage |  The proportion of space from the attached storage volumes to be used for MapR file system; the default is 100 \(all available space\); the minimum is 50\. Users who are doing large copies in and out of Amazon S3 storage using s3distcp or another such mechanism may see faster performance by allowing some of the storage space to be used as regular Linux storage\. For example: `--mfs-percentage,90`\. The remainder is mounted to S3\_TMP\_DIR as RAID0 file system\.  | 
| \-\-hive\-version |  The version of the Amazon Hive package\. The default is `latest`\. To disable the installation of Amazon Hive, use `--hive-version,none`\.  | 
| \-\-pig\-version |  The version of the Amazon Pig package\. The default is `latest`\. To disable the installation of Amazon Pig, use `--pig-version,none`\.  | 

The following table shows examples of commands, including arguments with the `--args` parameter, and which command MapR executes after interpreting the input\.


|  Options | Commands Processed by MapR | 
| --- | --- | 
| \-\-supported\-product mapr |  \-\-edition m3  | 
| \-\-supported\-product mapr\-m5 |  \-\-edition m5  | 
| \-\-supported\-product mapr\-m3 |  \-\-edition m3   | 
| \-\-with\-supported\-products mapr\-m3 \(deprecated\) |  \-\-edition m3  | 
| \-\-with\-supported\-products mapr\-m5 \(deprecated\) |  \-\-edition m5  | 
| \-\-supported\-product mapr\-m5 \-\-args "\-\-version,1\.1" |  \-\-edition m5 \-\-version 1\.1  | 
| \-\-supported\-product mapr\-m5 \-\-args "\-\-edition,m3" |  N/A \- returns an error  | 
| \-\-supported\-product mapr \-\-args "\-\-edition,m5" |  \-\-edition m5  | 
| \-\-supported\-product mapr \-\-args "\-\-version,1\.1" |  \-\-edition m3 \-\-version 1\.1  | 
| \-\-supported\-product mapr \-\-args "\-\-edition,m5,\-\-key1 value1" |  \-\-edition m5 \-\-key1 value1  | 

**Note**  
The `--with-supported-products `option is deprecated and replaced by the `--supported-product` option, which provides the same Hadoop and MapR versions with added support for parameters\.