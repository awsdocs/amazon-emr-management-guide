# Step 4: Check Cluster and Instance Health<a name="emr-troubleshoot-slow-4"></a>

 An Amazon EMR cluster is made up of nodes running on Amazon EC2 instances\. If those instances become resource\-bound \(such as running out of CPU or memory\), experience network connectivity issues, or are terminated, the speed of cluster processing suffers\. 

 There are up to three types of nodes in a cluster: 
+  **master node** — manages the cluster\. If it experiences a performance issue, the entire cluster is affected\. 
+  **core nodes** — process map\-reduce tasks and maintain the Hadoop Distributed Filesystem \(HDFS\)\. If one of these nodes experiences a performance issue, it can slow down HDFS operations as well as map\-reduce processing\. You can add additional core nodes to a cluster to improve performance, but cannot remove core nodes\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md)\. 
+  **task nodes** — process map\-reduce tasks\. These are purely computational resources and do not store data\. You can add task nodes to a cluster to speed up performance, or remove task nodes that are not needed\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md)\. 

 When you look at the health of a cluster, you should look at both the performance of the cluster overall, as well as the performance of individual instances\. There are several tools you can use: 

## Check Cluster Health with CloudWatch<a name="emr-troubleshoot-slow-4-cw"></a>

 Every Amazon EMR cluster reports metrics to CloudWatch\. These metrics provide summary performance information about the cluster, such as the total load, HDFS utilization, running tasks, remaining tasks, corrupt blocks, and more\. Looking at the CloudWatch metrics gives you the big picture about what is going on with your cluster and can provide insight into what is causing the slow down in processing\. In addition to using CloudWatch to analyze an existing performance issue, you can set alarms that cause CloudWatch to alert if a future performance issue occurs\. For more information, see [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\. 

## Check Job Status and HDFS Health<a name="emr-troubleshoot-slow-4-web-ui"></a>

Use the **Application history** on the cluster details page to view YARN application details\. For certain applications, you can drill into further detail and access logs directly\. This is particularly useful for Spark applications\. For more information, see [View Application History](emr-cluster-application-history.md)\.

Hadoop provides a series of web interfaces you can use to view information\. For more information about how to access these web interfaces, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\. 
+  JobTracker — provides information about the progress of job being processed by the cluster\. You can use this interface to identify when a job has become stuck\. 
+  HDFS NameNode — provides information about the percentage of HDFS utilization and available space on each node\. You can use this interface to identify when HDFS is becoming resource bound and requires additional capacity\. 
+  TaskTracker — provides information about the tasks of the job being processed by the cluster\. You can use this interface to identify when a task has become stuck\. 

## Check Instance Health with Amazon EC2<a name="emr-troubleshoot-slow-4-ec2"></a>

 Another way to look for information about the status of the instances in your cluster is to use the Amazon EC2 console\. Because each node in the cluster runs on an EC2 instance, you can use tools provided by Amazon EC2 to check their status\. For more information, see [View Cluster Instances in Amazon EC2](UsingEMR_Tagging.md)\. 