# Working With Amazon EMR\-Managed Security Groups<a name="emr-man-sec-groups"></a>

 Different managed security groups are associated with the master instance and with the core and task instances in a cluster\. An additional managed security group for service access is required when you create a cluster in a private subnet\. For more information about the role of managed security groups with respect to your network configuration, see [Amazon VPC Options](emr-clusters-in-a-vpc.md)\.

When you specify managed security groups for a cluster, you must use the same type of security group, default or custom, for all managed security groups\. For example, you can't specify a custom security group for the master instance, and then not specify a custom security group for core and task instances\.

If you use default managed security groups, you don't need to specify them when you create a cluster\. Amazon EMR automatically uses the defaults\. Moreover, if the defaults don't exist in the cluster's VPC yet, Amazon EMR creates them\. Amazon EMR also creates them if you explicitly specify them and they don't exist yet\.

You can edit rules in managed security groups after clusters are created\. When you create a new cluster, Amazon EMR checks the rules in the managed security groups that you specify, and then creates any missing rules that the new cluster needs in addition to rules that may have been added earlier\.

The default managed security groups are as follows:
+ **ElasticMapReduce\-master**

  For rules in this security group, see [Amazon EMR\-Managed Security Group for the Master Instance \(Public Subnets\)](#emr-sg-elasticmapreduce-master)\.
+ **ElasticMapReduce\-slave**

  For rules in this security group, see [Amazon EMR\-Managed Security Group for Core and Task Instances \(Public Subnets\)](#emr-sg-elasticmapreduce-slave)\.
+ **ElasticMapReduce\-Master\-Private**

  For rules in this security group, see [Amazon EMR\-Managed Security Group for the Master Instance \(Private Subnets\)](#emr-sg-elasticmapreduce-master-private)\.
+ **ElasticMapReduce\-Slave\-Private**

  For rules in this security group, see [Amazon EMR\-Managed Security Group for Core and Task Instances \(Private Subnets\)](#emr-sg-elasticmapreduce-slave-private)\.
+ **ElasticMapReduce\-ServiceAccess**

  For rules in this security group, see [Amazon EMR\-Managed Security Group for Service Access \(Private Subnets\)](#emr-sg-elasticmapreduce-sa-private)\.

## Amazon EMR\-Managed Security Group for the Master Instance \(Public Subnets\)<a name="emr-sg-elasticmapreduce-master"></a>

The default managed security group for the master instance in public subnets has the **Group Name** of **ElasticMapReduce\-master**\. The default managed security group has the following rules, and Amazon EMR adds the same rules if you specify a custom managed security group\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)

## Amazon EMR\-Managed Security Group for Core and Task Instances \(Public Subnets\)<a name="emr-sg-elasticmapreduce-slave"></a>

The default managed security group for core and task instances in public subnets has the **Group Name** of **ElasticMapReduce\-slave**\. The default managed security group has the following rules, and Amazon EMR adds the same rules if you specify a custom managed security group\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)

## Amazon EMR\-Managed Security Group for the Master Instance \(Private Subnets\)<a name="emr-sg-elasticmapreduce-master-private"></a>

The default managed security group for the master instance in private subnets has the **Group Name** of **ElasticMapReduce\-Master\-Private**\. The default managed security group has the following rules, and Amazon EMR adds the same rules if you specify a custom managed security group\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)

## Amazon EMR\-Managed Security Group for Core and Task Instances \(Private Subnets\)<a name="emr-sg-elasticmapreduce-slave-private"></a>

The default managed security group for core and task instances in private subnets has the **Group Name** of **ElasticMapReduce\-Slave\-Private**\. The default managed security group has the following rules, and Amazon EMR adds the same rules if you specify a custom managed security group\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)

## Amazon EMR\-Managed Security Group for Service Access \(Private Subnets\)<a name="emr-sg-elasticmapreduce-sa-private"></a>

The default managed security group for service access in private subnets has the **Group Name** of **ElasticMapReduce\-ServiceAccess**\. It has no inbound rules, and outbound rules that allow traffic over HTTPS \(port 8443\) to the other managed security groups in private subnets\. These rules allow the cluster manager to communicate with the master node and with core and task nodes\. The same rules are added if you specify a custom security group\.