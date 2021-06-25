# Plan and configure master nodes<a name="emr-plan-ha"></a>

When you launch an Amazon EMR cluster, you can choose to have one or three master nodes in your cluster\. Launching a cluster with three master nodes is only supported by Amazon EMR version 5\.23\.0 and later\. EMR can take advantage of EC2 placement groups to ensure master nodes are placed on distinct underlying hardware to further improve cluster availability\. For more information, see [EMR integration with EC2 placement groups](emr-plan-ha-placementgroup.md)\.

An EMR cluster with multiple master nodes provides the following key benefits:
+ The master node is no longer a single point of failure\. If one of the master nodes fails, the cluster uses the other two master nodes and runs without interruption\. In the meantime, Amazon EMR automatically replaces the failed master node with a new one that is provisioned with the same configuration and bootstrap actions\. 
+ EMR enables the Hadoop high availability features of HDFS NameNode and YARN ResourceManager and supports high availability for a few other open source applications\.

  For more information about how an EMR cluster with multiple master nodes supports open source applications and other EMR features, see [Supported applications and features](emr-plan-ha-applications.md)\.

**Note**  
The cluster can reside only in one Availability Zone or subnet\.

This section provides information about supported applications and features of an EMR cluster with multiple master nodes as well as the configuration details, best practices, and considerations for launching the cluster\.

**Topics**
+ [Supported applications and features](emr-plan-ha-applications.md)
+ [Launch an EMR Cluster with multiple master nodes](emr-plan-ha-launch.md)
+ [EMR integration with EC2 placement groups](emr-plan-ha-placementgroup.md)
+ [Considerations and best practices](emr-plan-ha-considerations.md)