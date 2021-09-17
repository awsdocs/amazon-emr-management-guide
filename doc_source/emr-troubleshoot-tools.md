# What tools are available for troubleshooting?<a name="emr-troubleshoot-tools"></a>

 There are several tools you can use to gather information about your cluster to help determine what went wrong\. Some require that you initialize them when you launch the cluster; others are available for every cluster\. 

**Topics**
+ [Tools to display cluster details](#emr-troubleshoot-tools-details)
+ [Tools to run scripts and configure processes](#emr-troubleshoot-tools-commands)
+ [Tools to view log files](#emr-troubleshoot-tools-logs)
+ [Tools to monitor cluster performance](#emr-troubleshoot-tools-performance)

## Tools to display cluster details<a name="emr-troubleshoot-tools-details"></a>

You can use the AWS Management Console, AWS CLI, or EMR API to retrieve detailed information about an EMR cluster and job execution\. For more information about using the AWS Management Console and AWS CLI, see [View cluster status and details](emr-manage-view-clusters.md)\.

### Amazon EMR console details pane<a name="emr-troubleshoot-tools-console"></a>

 In the **Clusters** list on the Amazon EMR console you can see high\-level information about the status of each cluster in your account and region\. The list displays all clusters that you have launched in the past two months, regardless of whether they are active or terminated\. From the **Clusters** list, you can select a cluster **Name** to view cluster details\. This information is organized in different categories to make it easy to navigate\. 

The **Application user interfaces** available in the cluster details page can be particularly useful for troubleshooting\. It provides status of YARN applications, and for some, such as Spark applications you can drill into different metrics and facets, such as jobs, stages, and executors\. For more information, see [View application history](emr-cluster-application-history.md)\. This feature is available only in Amazon EMR version 5\.8\.0 and later\.

### Amazon EMR command line interface<a name="emr-troubleshoot-tools-cli"></a>

 You can locate details about a cluster from the CLI using the `--describe` argument\. 

### Amazon EMR API<a name="emr-troubleshoot-tools-api"></a>

 You can locate details about a cluster from the API using the `DescribeJobFlows` action\. 

## Tools to run scripts and configure processes<a name="emr-troubleshoot-tools-commands"></a>

As part of your troubleshooting process, you might find it helpful to run custom scripts on your cluster or view and configure cluster processes\.

### View and restart application processes<a name="emr-troubleshoot-tools-stop-start-processes"></a>

It can be helpful to view running processes on your cluster in order to diagnose potential issues\. You can stop and restart cluster processes by connecting to the master node of your cluster\. For more information, see [Viewing and restarting Amazon EMR and application processes \(daemons\)](emr-process-restart-stop-view.md)\.

### Run commands and scripts without an SSH connection<a name="emr-troubleshoot-tools-run-scripts"></a>

To run a command or a script on your cluster as a step, you can use the `command-runner.jar` or `script-runner.jar` tools without establishing an SSH connection to the master node\. For more information, see [Run commands and scripts on an Amazon EMR cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-commandrunner.html)\.

## Tools to view log files<a name="emr-troubleshoot-tools-logs"></a>

Amazon EMR and Hadoop both generate log files as the cluster runs\. You can access these log files from several different tools, depending on the configuration you specified when you launched the cluster\. For more information, see [Configure cluster logging and debugging](emr-plan-debugging.md)\. 

### Log files on the master node<a name="emr-troubleshoot-tools-logs-master"></a>

Every cluster publishes logs files to the /mnt/var/log/ directory on the master node\. These log files are only available while the cluster is running\. 

### Log files archived to Amazon S3<a name="emr-troubleshoot-tools-logs-s3"></a>

 If you launch the cluster and specify an Amazon S3 log path, the cluster copies the log files stored in /mnt/var/log/ on the master node to Amazon S3 in 5\-minute intervals\. This ensures that you have access to the log files even after the cluster is terminated\. Because the files are archived in 5\-minute intervals, the last few minutes of an suddenly terminated cluster may not be available\. 

## Tools to monitor cluster performance<a name="emr-troubleshoot-tools-performance"></a>

 Amazon EMR provides several tools to monitor the performance of your cluster\. 

### Hadoop web interfaces<a name="emr-troubleshoot-tools-hadoop-ui"></a>

 Every cluster publishes a set of web interfaces on the master node that contain information about the cluster\. You can access these web pages by using an SSH tunnel to connect them on the master node\. For more information, see [View web interfaces hosted on Amazon EMR clusters](emr-web-interfaces.md)\. 

### CloudWatch metrics<a name="emr-troubleshoot-tools-cloudwatch"></a>

 Every cluster reports metrics to CloudWatch\. CloudWatch is a web service that tracks metrics, and which you can use to set alarms on those metrics\. For more information, see [Monitor metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\. 