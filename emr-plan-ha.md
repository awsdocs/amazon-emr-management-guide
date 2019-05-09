# Plan and Configure Master Nodes<a name="emr-plan-ha"></a>

When you launch an EMR cluster, you can choose to have one or three master nodes in your cluster\. Launching a cluster with three master nodes is only supported by Amazon EMR version 5\.23\.0 and later\. 

An EMR cluster with multiple master nodes provides the following key benefits:
+ The master node is no longer a single point of failure\. If one of the master nodes fails, the cluster uses the other two master nodes and runs without interruption\. In the meantime, Amazon EMR automatically replaces the failed master node with a new one that is provisioned with the same configuration and bootstrap actions\. 
+ EMR enables the Hadoop high availability features of HDFS NameNode and YARN ResourceManager and supports high availability for a few other open source applications\.

  For more information about how an EMR cluster with multiple master nodes supports open source applications and other EMR features, see [Supported Applications and Features](emr-plan-ha-applications.md)\.

**Note**  
The cluster can reside only in one Availability Zone or subnet\.

This section provides information about supported applications and features of an EMR cluster with multiple master nodes as well as the configuration details, best practices, and considerations for launching the cluster\.

**Topics**
+ [Supported Applications and Features](emr-plan-ha-applications.md)
+ [Launching an EMR Cluster with Multiple Master Nodes](emr-plan-ha-launch.md)
+ [Considerations and Best Practices](emr-plan-ha-considerations.md)