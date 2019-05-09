# Considerations and Best Practices<a name="emr-plan-ha-considerations"></a>

Limitations of an EMR cluster with multiple master nodes:
+ If any two master nodes fail simultaneously, EMR cannot recover the cluster\.
+ EMR clusters with multiple master nodes are not tolerant to Availability Zone failures\. In the case of an Availability Zone outage, you lose access to the EMR cluster\.
+ EMR does not guarantee the high availability features of open\-source applications other than the ones specified in [Supported Applications in an EMR Cluster with Multiple Master Nodes](emr-plan-ha-applications.md#emr-plan-ha-applications-list)\.

Considerations for configuring subnet:
+ An EMR cluster with multiple master nodes can reside only in one Availability Zone or subnet\. EMR cannot replace a failed master node if the subnet is fully utilized or oversubscribed in the event of a failover\. To avoid this scenario, it is recommended that you dedicate an entire subnet to an Amazon EMR cluster\. In addition, make sure that there are enough private IP addresses available in the subnet\.

Considerations for configuring core nodes:
+ To ensure the core node instance group is also highly available, it is recommended that you launch at least four core nodes\. If you decide to launch a smaller cluster with three or fewer core nodes, configure HDFS with sufficient DFS replication by setting `dfs.replication parameter` to at least 2\. For more information, see [HDFS Configuration](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hdfs-config.html)\.

Considerations for Setting Alarms on Metrics:
+ EMR currently does not provide application specific metrics about HDFS or YARN\. It is recommended that you set up alarms to monitor the master node instance count\. You can configure the alarms using the following CloudWatch metrics: `MultiMasterInstanceGroupNodesRunning`, `MultiMasterInstanceGroupNodesRunningPercentage`, or `MultiMasterInstanceGroupNodesRequested`\. You will be notified in the case of master node failure and replacement\. For example,
  + If the `MultiMasterInstanceGroupNodesRunningPercentage` is lower than 1\.0 and greater than 0\.5, the cluster may have lost a master node\. In this situation, EMR attempts to replace a master node\. 
  + If the `MultiMasterInstanceGroupNodesRunningPercentage` drops below 0\.5, two master nodes may have failed\. In this situation, the quorum is lost and the cluster cannot be recovered\. Manual intervention is required to migrate data off of this cluster\.

  For more information, see [Setting Alarms on Metrics](https://docs.aws.amazon.com/emr/latest/ManagementGuide/UsingEMR_ViewingMetrics.html#UsingEMR_ViewingMetrics_Alarm)\.