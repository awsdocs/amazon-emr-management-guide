# Amazon VPC Options<a name="emr-clusters-in-a-vpc"></a>

When you launch an Amazon EMR cluster within a VPC, you can launch it within either a public, private, or shared subnet\. There are slight but notable differences in configuration, depending on the subnet type you choose for a cluster\.

## Public Subnets<a name="emr-vpc-public-subnet"></a>

EMR clusters in a public subnet require a connected internet gateway\. This is because Amazon EMR clusters must access AWS services and Amazon EMR\. If a service, such as Amazon S3, provides the ability to create a VPC endpoint, you can access those services using the endpoint instead of accessing a public endpoint through an internet gateway\. Additionally, Amazon EMR cannot communicate with clusters in public subnets through a network address translation \(NAT\) device\. An internet gateway is required for this purpose but you can still use a NAT instance or gateway for other traffic in more complex scenarios\.

All instances in a cluster connect to Amazon S3 through either a VPC endpoint or internet gateway\. Other AWS services which do not currently support VPC endpoints use only an internet gateway\.

If you have additional AWS resources that you do not want connected to the internet gateway, you can launch those components in a private subnet that you create within your VPC\. 

Clusters running in a public subnet use two security groups: one for the master node and another for core and task nodes\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.

The following diagram shows how an Amazon EMR cluster runs in a VPC using a public subnet\. The cluster is able to connect to other AWS resources, such as Amazon S3 buckets, through the internet gateway\.

![\[Cluster on a VPC\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_default_v3a.png)

The following diagram shows how to set up a VPC so that a cluster in the VPC can access resources in your own network, such as an Oracle database\.

![\[Set up a VPC and cluster to access local VPN resources\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_withVPN_v3a.png)

## Private Subnets<a name="emr-vpc-private-subnet"></a>

Private subnets allow you to launch AWS resources without requiring the subnet to have an attached internet gateway\. This might be useful, for example, in an application that uses these private resources in the backend\. Those resources can then initiate outbound traffic using a NAT instance located in another subnet that has an internet gateway attached\. For more information about this scenario, see [Scenario 2: VPC with Public and Private Subnets \(NAT\)](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html)\. 

**Important**  
Amazon EMR only supports launching clusters in private subnets in releases 4\.2 or later\.

The following are differences from public subnets:
+ To access AWS services that do not provide a VPC endpoint, you still must use a NAT instance or an internet gateway\.
+ At a minimum, you must provide a route to the Amazon EMR service logs bucket and Amazon Linux repository in Amazon S3\. For more information, see [Minimum Amazon S3 Policy for Private Subnet](private-subnet-iampolicy.md)
+ If you use EMRFS features, you need to have an Amazon S3 VPC endpoint and a route from your private subnet to DynamoDB\.
+ Debugging only works if you provide a route from your private subnet to a public Amazon SQS endpoint\.
+ Creating a private subnet configuration with a NAT instance or gateway in a public subnet is only supported using the AWS Management Console\. The easiest way to add and configure NAT instances and Amazon S3 VPC endpoints for EMR clusters is to use the **VPC Subnets List** page in the Amazon EMR console\. To configure NAT gateways, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\.
+ You cannot change a subnet with an existing EMR cluster from public to private or vice versa\. To locate an EMR cluster within a private subnet, the cluster must be started in that private subnet\. 

Amazon EMR creates and uses different default security groups for the clusters in a private subnet: ElasticMapReduce\-Master\-Private, ElasticMapReduce\-Slave\-Private, and ElasticMapReduce\-ServiceAccess\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.

For a complete listing of NACLs of your cluster, choose **Security groups for Master** and **Security groups for Core & Task** on the Amazon EMR console **Cluster Details** page\.

The following image shows how an EMR cluster is configured within a private subnet\. The only communication outside the subnet is to Amazon EMR\. 

![\[Launch an EMR cluster in a private subnet\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_with_private_subnet_v3a.png)

The following image shows a sample configuration for an EMR cluster within a private subnet connected to a NAT instance that is residing in a public subnet\.

![\[Private subnet with NAT\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc_private_subnet_nat_v3a.png)

## Shared Subnets<a name="emr-vpc-shared-subnet"></a>

VPC sharing allows customers to share subnets with other AWS accounts within the same AWS Organization\. You can launch Amazon EMR clusters into both public shared and private shared subnets, with the following caveats\.

The subnet owner must share a subnet with you before you can launch an Amazon EMR cluster into it\. However, shared subnets can later be unshared\. For more information, see [Working with Shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html)\. When a cluster is launched into a shared subnet and that shared subnet is then unshared, you can observe specific behaviors based on the state of the Amazon EMR cluster when the subnet is unshared\.
+ Subnet is unshared *before* the cluster is successfully launched \- If the owner stops sharing the Amazon VPC or subnet while the participant is launching a cluster, the cluster could fail to start or be partially initialized without provisioning all requested instances\. 
+ Subnet is unshared *after* the cluster is successfully launched \- When the owner stops sharing a subnet or Amazon VPC with the participant, the participant's clusters will not be able to resize to add new instances or to replace unhealthy instances\.

When you launch an Amazon EMR cluster, multiple security groups are created\. In a shared subnet, the subnet participant controls these security groups\. The subnet owner can see these security groups but cannot perform any actions on them\. If the subnet owner wants to remove or modify the security group, the participant that created the security group must take the action\.