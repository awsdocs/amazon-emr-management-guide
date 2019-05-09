# What Tools are Available for Troubleshooting?<a name="emr-troubleshoot-tools"></a>

 There are several tools you can use to gather information about your cluster to help determine what went wrong\. Some require that you initialize them when you launch the cluster; others are available for every cluster\. 

**Topics**
+ [Tools to Display Cluster Details](#emr-troubleshoot-tools-details)
+ [Tools to View Log Files](#emr-troubleshoot-tools-logs)
+ [Tools to Monitor Cluster Performance](#emr-troubleshoot-tools-performance)

## Tools to Display Cluster Details<a name="emr-troubleshoot-tools-details"></a>

You can use the AWS Management Console, AWS CLI, or EMR API to retrieve detailed information about an EMR cluster and job execution\. For more information about using the AWS Management Console and AWS CLI, see [View Cluster Status and Details](emr-manage-view-clusters.md)\.

### Amazon EMR Console Details Pane<a name="emr-troubleshoot-tools-console"></a>

 In the **Clusters** list on the Amazon EMR console you can see high\-level information about the status of each cluster in your account and region\. The list displays all clusters that you have launched in the past two months, regardless of whether they are active or terminated\. From the **Clusters** list, you can select a cluster **Name** to view cluster details\. This information is organized in different categories to make it easy to navigate\. 

The **Application history** available in the cluster details page can be particularly useful for troubleshooting\. It provides status of YARN applications, and for some, such as Spark applications you can drill into different metrics and facets, such as jobs, stages, and executors\. For more information, see [View Application History](emr-cluster-application-history.md)\. This feature is available only in Amazon EMR version 5\.8\.0 and later\.

### Amazon EMR Command Line Interface<a name="emr-troubleshoot-tools-cli"></a>

 You can locate details about a cluster from the CLI using the `--describe` argument\. 

### Amazon EMR API<a name="emr-troubleshoot-tools-api"></a>

 You can locate details about a cluster from the API using the `DescribeJobFlows` action\. 

## Tools to View Log Files<a name="emr-troubleshoot-tools-logs"></a>

 Amazon EMR and Hadoop both generate log files as the cluster runs\. You can access these log files from several different tools, depending on the configuration you specified when you launched the cluster\. For more information, see [Configure Cluster Logging and Debugging](emr-plan-debugging.md)\. 

### Log Files on the Master Node<a name="emr-troubleshoot-tools-logs-master"></a>

 Every cluster publishes logs files to the /mnt/var/log/ directory on the master node\. These log files are only available while the cluster is running\. 

### Log Files Archived to Amazon S3<a name="emr-troubleshoot-tools-logs-s3"></a>

 If you launch the cluster and specify an Amazon S3 log path, the cluster copies the log files stored in /mnt/var/log/ on the master node to Amazon S3 in 5\-minute intervals\. This ensures that you have access to the log files even after the cluster is terminated\. Because the files are archived in 5\-minute intervals, the last few minutes of an suddenly terminated cluster may not be available\. 

## Tools to Monitor Cluster Performance<a name="emr-troubleshoot-tools-performance"></a>

 Amazon EMR provides several tools to monitor the performance of your cluster\. 

### Hadoop Web Interfaces<a name="emr-troubleshoot-tools-hadoop-ui"></a>

 Every cluster publishes a set of web interfaces on the master node that contain information about the cluster\. You can access these web pages by using an SSH tunnel to connect them on the master node\. For more information, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\. 

### CloudWatch Metrics<a name="emr-troubleshoot-tools-cloudwatch"></a>

 Every cluster reports metrics to CloudWatch\. CloudWatch is a web service that tracks metrics, and which you can use to set alarms on those metrics\. For more information, see [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\. 