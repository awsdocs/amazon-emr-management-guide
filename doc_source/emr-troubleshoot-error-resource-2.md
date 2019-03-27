# File could only be replicated to 0 nodes instead of 1<a name="emr-troubleshoot-error-resource-2"></a>

When a file is written to HDFS, it is replicated to multiple core nodes\. When you see this error, it means that the NameNode daemon does not have any available DataNode instances to write data to in HDFS\. In other words, block replication is not taking place\. This error can be caused by a number of issues: 
+ The HDFS filesystem may have run out of space\. This is the most likely cause\. 
+ DataNode instances may not have been available when the job was run\. 
+ DataNode instances may have been blocked from communication with the master node\. 
+ Instances in the core instance group might not be available\. 
+ Permissions may be missing\. For example, the JobTracker daemon may not have permissions to create job tracker information\. 
+ The reserved space setting for a DataNode instance may be insufficient\. Check whether this is the case by checking the dfs\.datanode\.du\.reserved configuration setting\. 

To check whether this issue is caused by HDFS running out of disk space, look at the `HDFSUtilization` metric in CloudWatch\. If this value is too high, you can add additional core nodes to the cluster\. If you have a cluster that you think might run out of HDFS disk space, you can set an alarm in CloudWatch to alert you when the value of `HDFSUtilization` rises above a certain level\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md) and [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\. 

If HDFS running out of space was not the issue, check the DataNode logs, the NameNode logs and network connectivity for other issues that could have prevented HDFS from replicating data\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 