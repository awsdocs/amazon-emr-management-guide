# Import files with Distributed Cache<a name="emr-plan-input-distributed-cache"></a>

**Topics**
+ [Supported File Types](#emr-dev-supported-file-types)
+ [Location of Cached Files](#locationofcache)
+ [Access Cached Files From Streaming Applications](#cachemapper)
+ [Access Cached Files From Streaming Applications Using the Amazon EMR Console](#cacheinconsole)
+ [Access Cached Files From Streaming Applications Using the AWS CLI](#cacheinruby)

Distributed Cache is a Hadoop feature that can boost efficiency when a map or a reduce task needs access to common data\. If your cluster depends on existing applications or binaries that are not installed when the cluster is created, you can use Distributed Cache to import these files\. This feature lets a cluster node read the imported files from its local file system, instead of retrieving the files from other cluster nodes\. 

For more information, go to [http://hadoop\.apache\.org/docs/stable/api/org/apache/hadoop/filecache/DistributedCache\.html](http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/filecache/DistributedCache.html)\.

You invoke Distributed Cache when you create the cluster\. The files are cached just before starting the Hadoop job and the files remain cached for the duration of the job\. You can cache files stored on any Hadoop\-compatible file system, for example HDFS or Amazon S3\. The default size of the file cache is 10GB\. To change the size of the cache, reconfigure the Hadoop parameter, `local.cache.size` using the bootstrap action\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\.

## Supported File Types<a name="emr-dev-supported-file-types"></a>

Distributed Cache allows both single files and archives\. Individual files are cached as read only\. Executables and binary files have execution permissions set\.

Archives are one or more files packaged using a utility, such as `gzip`\. Distributed Cache passes the compressed files to each core node and decompresses the archive as part of caching\. Distributed Cache supports the following compression formats:
+ zip
+ tgz
+ tar\.gz
+ tar
+ jar

## Location of Cached Files<a name="locationofcache"></a>

Distributed Cache copies files to core nodes only\. If there are no core nodes in the cluster, Distributed Cache copies the files to the master node\.

Distributed Cache associates the cache files to the current working directory of the mapper and reducer using symlinks\. A symlink is an alias to a file location, not the actual file location\. The value of the parameter, `yarn.nodemanager.local-dirs` in `yarn-site.xml`, specifies the location of temporary files\. Amazon EMR sets this parameter to , `/mnt/mapred`, or some variation based on instance type and EMR version\. For example, a setting may have `/mnt/mapred` and `/mnt1/mapred` because the instance type has two ephemeral volumes\. Cache files are located in a subdirectory of the temporary file location at `/mnt/mapred/taskTracker/archive`\. 

If you cache a single file, Distributed Cache puts the file in the `archive` directory\. If you cache an archive, Distributed Cache decompresses the file, creates a subdirectory in `/archive` with the same name as the archive file name\. The individual files are located in the new subdirectory\.

You can use Distributed Cache only when using Streaming\.

## Access Cached Files From Streaming Applications<a name="cachemapper"></a>

To access the cached files from your mapper or reducer applications, make sure that you have added the current working directory \(\./\) into your application path and referenced the cached files as though they are present in the current working directory\.

## Access Cached Files From Streaming Applications Using the Amazon EMR Console<a name="cacheinconsole"></a>

You can use the Amazon EMR console to create clusters that use Distributed Cache\. 

**To specify Distributed Cache files using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Step execution** as the Launch mode\.

1. In the **Steps** section, in the **Add step** field, choose **Streaming program** from the list and click **Configure and add**\.

1. In the **Arguments** field, include the files and archives to save to the cache and click **Add**\.

   The size of the file \(or total size of the files in an archive file\) must be less than the allocated cache size\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-input-distributed-cache.html)

1. Proceed with configuring and launching your cluster\. Your cluster copies the files to the cache location before processing any cluster steps\.

## Access Cached Files From Streaming Applications Using the AWS CLI<a name="cacheinruby"></a>

You can use the CLI to create clusters that use Distributed Cache\. 

**To specify Distributed Cache files using the AWS CLI**
+ To submit a Streaming step when a cluster is created, type the `create-cluster` command with the `--steps` parameter\. To specify Distributed Cache files using the AWS CLI, specify the appropriate arguments when submitting a Streaming step\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-input-distributed-cache.html)

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**Example 1**  
Type the following command to launch a cluster and submit a Streaming step that uses `-cacheFile` to add one file, `sample_dataset_cached.dat`, to the cache\.   

```
aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-type m5.xlarge --instance-count 3 --steps Type=STREAMING,Name="Streaming program",ActionOnFailure=CONTINUE,Args=["--files","s3://my_bucket/my_mapper.py s3://my_bucket/my_reducer.py","-mapper","my_mapper.py","-reducer","my_reducer.py,"-input","s3://my_bucket/my_input","-output","s3://my_bucket/my_output", "-cacheFile","s3://my_bucket/sample_dataset.dat#sample_dataset_cached.dat"]
```
When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

**Example 2**  
The following command shows the creation of a streaming cluster and uses `-cacheArchive` to add an archive of files to the cache\.   

```
aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-type m5.xlarge --instance-count 3 --steps Type=STREAMING,Name="Streaming program",ActionOnFailure=CONTINUE,Args=["--files","s3://my_bucket/my_mapper.py s3://my_bucket/my_reducer.py","-mapper","my_mapper.py","-reducer","my_reducer.py,"-input","s3://my_bucket/my_input","-output","s3://my_bucket/my_output", "-cacheArchive","s3://my_bucket/sample_dataset.tgz#sample_dataset_cached"]
```
When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.