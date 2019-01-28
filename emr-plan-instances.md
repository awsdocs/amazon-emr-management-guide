# Configure Cluster Hardware and Networking<a name="emr-plan-instances"></a>

An important consideration when you create an EMR cluster is how you configure Amazon EC2 instances and network options\. EC2 instances in an EMR cluster are organized into *node types*\. There are three: the *master node*, the *core node*, and *task nodes*\. Each node type performs a set of roles defined by the distributed applications that you install on the cluster\. During a Hadoop MapReduce or Spark job, for example, components on core and task nodes process data, transfer output to Amazon S3 or HDFS, and provide status metadata back to the master node\. With a single\-node cluster, all components run on the master node\.

The collection of EC2 instances that host each node type is called either an *instance fleet* or a *uniform instance group*\. The instance fleets or uniform instance groups configuration is a choice you make when you create a cluster\. It applies to all node types, and it can't be changed later\.

When you create a cluster, you make choices that ultimately determine the performance profile of your cluster\. This chapter covers those options in detail, and then ties them all together with best practices and guidelines\.

**Note**  
The instance fleets configuration is available only in Amazon EMR release versions 4\.8\.0 and later, excluding 5\.0\.0 and 5\.0\.3\.

**Topics**
+ [Understanding Master, Core, and Task Nodes](emr-master-core-task-nodes.md)
+ [Configure EC2 Instances](emr-plan-ec2-instances.md)
+ [Configure Networking](emr-plan-vpc-subnet.md)
+ [Create a Cluster with Instance Fleets or Uniform Instance Groups](emr-instance-group-configuration.md)
+ [Cluster Configuration Guidelines and Best Practices](emr-plan-instances-guidelines.md)