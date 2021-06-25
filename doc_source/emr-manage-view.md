# View and monitor a cluster<a name="emr-manage-view"></a>

 Amazon EMR provides several tools you can use to gather information about your cluster\. You can access information about the cluster from the console, the CLI or programmatically\. The standard Hadoop web interfaces and log files are available on the master node\. You can also use monitoring services such as CloudWatch and Ganglia to track the performance of your cluster\. 

Application history is also available from the console using the "persistent" application UIs for Spark History Server starting with Amazon EMR 5\.25\.0\. With Amazon EMR 6\.x, persistent YARN timeline server, and Tez user interfaces are also available\. These services are hosted off\-cluster, so you can access application history for 30 days after the cluster terminates, without the need for a SSH connection or web proxy\. See [View application history](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-cluster-application-history.html)\.

**Topics**
+ [View cluster status and details](emr-manage-view-clusters.md)
+ [Enhanced step debugging](emr-enhanced-step-debugging.md)
+ [View application history](emr-cluster-application-history.md)
+ [View log files](emr-manage-view-web-log-files.md)
+ [View cluster instances in Amazon EC2](UsingEMR_Tagging.md)
+ [CloudWatch events and metrics](emr-manage-cluster-cloudwatch.md)
+ [View cluster application metrics with Ganglia](ViewingGangliaMetrics.md)
+ [Logging Amazon EMR API calls in AWS CloudTrail](logging_emr_api_calls.md)