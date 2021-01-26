# View and Monitor a Cluster<a name="emr-manage-view"></a>

 Amazon EMR provides several tools you can use to gather information about your cluster\. You can access information about the cluster from the console, the CLI or programmatically\. The standard Hadoop web interfaces and log files are available on the master node\. You can also use monitoring services such as CloudWatch and Ganglia to track the performance of your cluster\. 

Application history is also available from the console using the "persistent" application UIs for Spark History Server starting with Amazon EMR 5\.25\.0\. With Amazon EMR 6\.x, persistent YARN timeline server, and Tez user interfaces are also available\. These services are hosted off\-cluster, so you can access application history for 30 days after the cluster terminates, without the need for a SSH connection or web proxy\. See [View Application History](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-cluster-application-history.html)\.

**Topics**
+ [View Cluster Status and Details](emr-manage-view-clusters.md)
+ [Enhanced Step Debugging](emr-enhanced-step-debugging.md)
+ [View Application History](emr-cluster-application-history.md)
+ [View Log Files](emr-manage-view-web-log-files.md)
+ [View Cluster Instances in Amazon EC2](UsingEMR_Tagging.md)
+ [CloudWatch Events and Metrics](emr-manage-cluster-cloudwatch.md)
+ [View Cluster Application Metrics with Ganglia](ViewingGangliaMetrics.md)
+ [Logging Amazon EMR API Calls in AWS CloudTrail](logging_emr_api_calls.md)