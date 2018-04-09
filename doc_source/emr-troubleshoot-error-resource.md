# Resource Errors<a name="emr-troubleshoot-error-resource"></a>

The following errors are commonly caused by constrained resources on the cluster\.

**Topics**
+ [Do you have enough HDFS space for your cluster?](#enough-hdfs-space)
+ [Are you seeing "EC2 Quota Exceeded" errors?](#emr-dev-terminate-time)
+ [Are you seeing "Too many fetch\-failures" errors?](#emr-troubleshoot-error-resource-1)
+ [Are you seeing "File could only be replicated to 0 nodes instead of 1" errors?](#emr-troubleshoot-error-resource-2)
+ [Are your TaskTracker nodes being blacklisted?](#emr-troubleshoot-error-resource-3)

## Do you have enough HDFS space for your cluster?<a name="enough-hdfs-space"></a>

If you do not, Amazon EMR returns the following error: **“Cannot replicate block, only managed to replicate to zero nodes\.”** This error occurs when you generate more data in your cluster than can be stored in HDFS\. You will see this error only while the cluster is running, because when the cluster ends it releases the HDFS space it was using\. 

The amount of HDFS space available to a cluster depends on the number and type of Amazon EC2 instances that are used as core nodes\. All of the disk space on each Amazon EC2 instance is available to be used by HDFS\. For more information about the amount of local storage for each EC2 instance type, see [Instance Types and Families](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide//instance-types.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

The other factor that can affect the amount of HDFS space available is the replication factor, which is the number of copies of each data block that are stored in HDFS for redundancy\. The replication factor increases with the number of nodes in the cluster: there are 3 copies of each data block for a cluster with 10 or more nodes, 2 copies of each block for a cluster with 4 to 9 nodes, and 1 copy \(no redundancy\) for clusters with 3 or fewer nodes\. The total HDFS space available is divided by the replication factor\. In some cases, such as increasing the number of nodes from 9 to 10, the increase in replication factor can actually cause the amount of available HDFS space to decrease\. 

For example, a cluster with ten core nodes of type m1\.large would have 2833 GB of space available to HDFS \(\(10 nodes X 850 GB per node\)/replication factor of 3\)\. 

If your cluster exceeds the amount of space available to HDFS, you can add additional core nodes to your cluster or use data compression to create more HDFS space\. If your cluster is one that can be stopped and restarted, you may consider using core nodes of a larger Amazon EC2 instance type\. You might also consider adjusting the replication factor\. Be aware, though, that decreasing the replication factor reduces the redundancy of HDFS data and your cluster's ability to recover from lost or corrupted HDFS blocks\. 

## Are you seeing "EC2 Quota Exceeded" errors?<a name="emr-dev-terminate-time"></a>

If you are getting messages that you are exceeding your Amazon EC2 instance quota, this may be for one of several reasons\. Depending on configuration differences, it may take up to 5\-20 minutes for previous clusters to terminate and release allocated resources\. If you are getting an `EC2 QUOTA EXCEEDED` error when you attempt to launch a cluster, it may be because resources from a recently terminated cluster have not yet been released\. Furthermore, if you attempt to resize an instance group, you may also encounter this error when your new target size is greater than the current instance quota for the account\. In these cases, you can either terminate and launch the cluster with a smaller target size\. In all cases, you can terminate unused cluster resources or EC2 instances, [request that your Amazon EC2 quota be increased](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances), or wait to re\-launch a cluster\. 

**Note**  
You cannot issue more than one resize request to the same cluster\. Therefore, if your first request fails, you will have to potentially terminate your current cluster and launch another cluster to with the number of instances desired\. 

## Are you seeing "Too many fetch\-failures" errors?<a name="emr-troubleshoot-error-resource-1"></a>

The presence of "**Too many fetch\-failures**" or "**Error reading task output**" error messages in step or task attempt logs indicates the running task is dependent on the output of another task\. This often occurs when a reduce task is queued to execute and requires the output of one or more map tasks and the output is not yet available\. 

There are several reasons the output may not be available: 
+ The prerequisite task is still processing\. This is often a map task\. 
+ The data may be unavailable due to poor network connectivity if the data is located on a different instance\. 
+ If HDFS is used to retrieve the output, there may be an issue with HDFS\. 

The most common cause of this error is that the previous task is still processing\. This is especially likely if the errors are occurring when the reduce tasks are first trying to run\. You can check whether this is the case by reviewing the syslog log for the cluster step that is returning the error\. If the syslog shows both map and reduce tasks making progress, this indicates that the reduce phase has started while there are map tasks that have not yet completed\. 

One thing to look for in the logs is a map progress percentage that goes to 100% and then drops back to a lower value\. When the map percentage is at 100%, this does not mean that all map tasks are completed\. It simply means that Hadoop is executing all the map tasks\. If this value drops back below 100%, it means that a map task has failed and, depending on the configuration, Hadoop may try to reschedule the task\. If the map percentage stays at 100% in the logs, look at the CloudWatch metrics, specifically `RunningMapTasks`, to check whether the map task is still processing\. You can also find this information using the Hadoop web interface on the master node\. 

If you are seeing this issue, there are several things you can try:
+ Instruct the reduce phase to wait longer before starting\. You can do this by altering the Hadoop configuration setting mapred\.reduce\.slowstart\.completed\.maps to a longer time\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\. 
+ Match the reducer count to the total reducer capability of the cluster\. You do this by adjusting the Hadoop configuration setting mapred\.reduce\.tasks for the job\. 
+ Use a combiner class code to minimize the amount of outputs that need to be fetched\. 
+ Check that there are no issues with the Amazon EC2 service that are affecting the network performance of the cluster\. You can do this using the [Service Health Dashboard](http://status.aws.amazon.com//)\. 
+ Review the CPU and memory resources of the instances in your cluster to make sure that your data processing is not overwhelming the resources of your nodes\. For more information, see [Configure Cluster Hardware and Networking](emr-plan-instances.md)\. 
+ Check the version of the Amazon Machine Image \(AMI\) used in your Amazon EMR cluster\. If the version is 2\.3\.0 through 2\.4\.4 inclusive, update to a later version\. AMI versions in the specified range use a version of Jetty that may fail to deliver output from the map phase\. The fetch error occurs when the reducers cannot obtain output from the map phase\.

  Jetty is an open\-source HTTP server that is used for machine to machine communications within a Hadoop cluster\.

## Are you seeing "File could only be replicated to 0 nodes instead of 1" errors?<a name="emr-troubleshoot-error-resource-2"></a>

When a file is written to HDFS, it is replicated to multiple core nodes\. When you see this error, it means that the NameNode daemon does not have any available DataNode instances to write data to in HDFS\. In other words, block replication is not taking place\. This error can be caused by a number of issues: 
+ The HDFS filesystem may have run out of space\. This is the most likely cause\. 
+ DataNode instances may not have been available when the job was run\. 
+ DataNode instances may have been blocked from communication with the master node\. 
+ Instances in the core instance group might not be available\. 
+ Permissions may be missing\. For example, the JobTracker daemon may not have permissions to create job tracker information\. 
+ The reserved space setting for a DataNode instance may be insufficient\. Check whether this is the case by checking the dfs\.datanode\.du\.reserved configuration setting\. 

To check whether this issue is caused by HDFS running out of disk space, look at the `HDFSUtilization` metric in CloudWatch\. If this value is too high, you can add additional core nodes to the cluster\. Keep in mind that you can only add core nodes to a cluster, you cannot remove them\. If you have a cluster that you think might run out of HDFS disk space, you can set an alarm in CloudWatch to alert you when the value of `HDFSUtilization` rises above a certain level\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md) and [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\. 

If HDFS running out of space was not the issue, check the DataNode logs, the NameNode logs and network connectivity for other issues that could have prevented HDFS from replicating data\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

## Are your TaskTracker nodes being blacklisted?<a name="emr-troubleshoot-error-resource-3"></a>

A TaskTracker node is a node in the cluster that accepts map and reduce tasks\. These are assigned by a JobTracker daemon\. The JobTracker monitors the TaskTracker node through a heartbeat\. 

There are a couple of situations in which the JobTracker daemon blacklists a TaskTacker node, removing it from the pool of nodes available to process tasks: 
+ If the TaskTracker node has not sent a heartbeat to the JobTracker daemon in the past 10 minutes \(60000 milliseconds\)\. This time period can be configured using the `mapred.tasktracker.expiry.interval` configuration setting\. For more information about changing Hadoop configuration settings, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\. 
+ If the TaskTracker node has more than 4 failed tasks\. You can change this to a higher value using the `modify mapred.max.tracker.failures` configuration parameter\. Other configuration settings you might want to change are the settings that control how many times to attempt a task before marking it as failed: `mapred.map.max.attempts` for map tasks and `mapreduce.reduce.maxattempts` for reduce tasks\. For more information about changing Hadoop configuration settings, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\. 

You can use the CloudWatch CLI to view the number of blacklisted TaskTracker nodes\. The command for doing so is shown in the following example\. For more information, see the [Amazon CloudWatch CLI Reference](http://docs.aws.amazon.com/AmazonCloudWatch/latest/cli//CLIReference.html)\. 

```
mon-get-stats NoOfBlackListedTaskTrackers --dimensions JobFlowId=JobFlowID --statistics Maximum --namespace AWS/ElasticMapReduce
```

The following example shows how to launch a cluster and use a bootstrap action to set the value of `mapred.max.tracker.failures` to 7, instead of the default 4\.

Type the following command using the AWS CLI and replace *myKey* with the name of your EC2 key pair\. 

```
aws emr create-cluster --name "Test cluster" --ami-version 2.4 --applications Name=Hive Name=Pig \
--use-default-roles --ec2-attributes KeyName=myKey \
--instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=CORE,InstanceCount=2,InstanceType=m3.xlarge \
--bootstrap-actions Path=s3://elasticmapreduce/bootstrap-actions/configure-hadoop,Name="Modified  mapred.max.tracker.failures",Args=["-m","mapred.max.tracker.failures=7"]
```

**Note**  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

When you launch a cluster using the preceding example, you can connect to the master node and see the changed configuration setting in `/home/hadoop/conf/mapred-site.xml`\. The modified line will appear as shown in the following example\. 

```
<property><name>mapred.max.tracker.failures</name><value>7</value></property>
```