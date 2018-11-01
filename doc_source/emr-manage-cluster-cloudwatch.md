# CloudWatch Events and Metrics<a name="emr-manage-cluster-cloudwatch"></a>

You can use events and metrics to track the activity and health of an Amazon EMR cluster, viewing events and metrics quickly in the Amazon EMR console for a single cluster, and viewing events for all clusters in a region\. You can use CloudWatch Events to define an action to take when Amazon EMR generates an event that matches a pattern that you specify, and you can also use CloudWatch to monitor metrics\.

Events are useful for monitoring a specific occurrence within a cluster—for example, when a cluster changes state from starting to running\. Metrics are useful for monitoring a specific value—for example, the percentage of available disk space that HDFS is using within a cluster\.

For more information about CloudWatch Events, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\. For more information about CloudWatch metrics, see [Using Amazon CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) and [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.

**Topics**
+ [Monitor CloudWatch Events](emr-manage-cloudwatch-events.md)
+ [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)