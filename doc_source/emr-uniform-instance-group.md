# Configure Uniform Instance Groups<a name="emr-uniform-instance-group"></a>

With the instance groups configuration, each node type \(master, core, or task\) consists of the same instance type and the same purchasing option for instances: On\-Demand or Spot\. You specify these settings when you create an instance group and they can't be changed later\. You can, however, add instances of the same type and purchasing option to core and task instance groups\. You can also remove instances\.

To add different instance types after a cluster is created, you can add additional task instance groups, specifying different instance types and purchasing options for each instance group\. For more information, see [Scaling Cluster Resources](emr-scale-on-demand.md)\.

This section covers creating a cluster with uniform instance groups\. For more information about modifying an existing instance group by adding or removing instances manually or with automatic scaling, see [Manage Clusters](emr-manage.md)\.

## Use the Console to Configure Uniform Instance Groups<a name="emr-uniform-instance-group-console"></a>

The following procedure covers **Advanced options** when you create a cluster\. Using **Quick options** also creates a cluster with the instance groups configuration\. For more information about using **Quick Options**, see the Getting Started tutorial\.

**To create a cluster with uniform instance groups using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**, enter **Software Configuration** options, and then choose **Next**\.

1. In the **Hardware Configuration** screen, leave **Uniform instance groups** selected\.

1. Choose the **Network**, and then choose the **EC2 Subnet** in which you want your cluster to run\. For more information about VPCs and subnets, see [Plan and Configure Networking](emr-plan-vpc-subnet.md)\.
**Note**  
Your account and region may give you the option to choose **Launch into EC2\-Classic** for **Network**\. If you choose that option, choose an **EC2 Availability Zone** rather than an **EC2 Subnet**\. For more information, see [Amazon EC2 and Amazon VPC](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Within each **Node type** row:
   + Under **Node type**, if you want to change the default name of the instance group, click the pencil icon and then enter a friendly name\. If want to remove the **Task** instance group, click the X icon or choose **Add task instance group** to add additional **Task** instance groups\.
   + Under **Instance type** click the pencil icon and then choose the instance type you want to use for that node type\.
**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.
   + Under **Instance count**, enter the number of instances on which to run that node type\. There is only one instance in the **Master** node type\.
   + Under **Purchasing option** choose **On\-demand**, or choose **Spot** and enter the **Maximum Spot price** you are willing to pay per instance\. Instances in this instance group launch when the Spot price in the Availability Zone of the **EC2 Subnet ** you chose is below the **Maximum Spot price**\.
**Tip**  
Mouse over the information tooltip for **Maximum Spot price** to see the Spot price for all Availability Zones in the current region\. The lowest Spot price is in green\. You might want to use this information to inform your **EC2 Subnet** selection\.
   + Under **Auto Scaling for Core and Task node types**, choose the pencil icon, and then configure the automatic scaling options\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.

1. To add another task instance group to the cluster, click and configure settings for the instance group as described in the previous step\.

1. Choose **Next**, modify other cluster settings, and then launch the cluster\.

## Use the AWS CLI to Create a Cluster with Uniform Instance Groups<a name="emr-uniform-instance-group-cli"></a>

To specify the instance groups configuration for a cluster using the AWS CLI, use the `create-cluster` command along with the `--instance-groups` parameter\. Amazon EMR assumes the On\-Demand purchasing option unless you specify the `BidPrice` argument for an instance group\. For examples of `create-cluster` commands that launch uniform instance groups with On\-Demand Instances and a variety of cluster options, type `aws emr create-cluster help `at the command line, or see [create\-cluster](http://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html) in the *AWS CLI Command Reference*\.

You can use the AWS CLI to create uniform instance groups in a cluster that use Spot Instances\. The offered Spot price depends on Availability Zone\. When you use the CLI or API, you can specify the Availability Zone either with the `AvailabilityZone` argument \(if you're using an EC2\-classic network\) or the `SubnetID `argument of the `--ec2-attributes `parameter\. The Availability Zone or subnet that you select applies to the cluster, so it's used for all instance groups\. If you don't specify an Availability Zone or subnet explicitly, Amazon EMR selects the Availability Zone with the lowest Spot price when it launches the cluster\.

The following example demonstrates a `create-cluster` command that creates master, core, and two task instance groups that all use Spot Instances\. Replace *myKey* with the name of your EC2 key pair\. 

**Note**  
If you have not previously created the default Amazon EMR service role and Amazon EC2 instance profile, use `aws emr create-default-roles` to create them before using the `create-cluster` command\.

Linux, UNIX, and macOS users:

```
1. aws emr create-cluster --name "MySpotCluster" --release-label emr-5.13.0 \
2. --use-default-roles --ec2-attributes KeyName=myKey \
3. --instance-groups InstanceGroupType=MASTER,InstanceType=m3.xlarge,InstanceCount=1,BidPrice=0.25 \
4. InstanceGroupType=CORE,InstanceType=m3.xlarge,InstanceCount=2,BidPrice=0.03 \ 
5. InstanceGroupType=TASK,InstanceType=m3.xlarge,InstanceCount=4,BidPrice=0.03 \
6. InstanceGroupType=TASK,InstanceType=m3.xlarge,InstanceCount=2,BidPrice=0.04
```

Windows users:

```
1. aws emr create-cluster --name "Spot cluster" --release-label emr-5.13.0 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-groups InstanceGroupType=MASTER,InstanceType=m3.xlarge,InstanceCount=1,BidPrice=0.25 InstanceGroupType=CORE,BidPrice=0.03,InstanceType=m3.xlarge,InstanceCount=2 InstanceGroupType=TASK,BidPrice=0.03,InstanceType=m3.xlarge,InstanceCount=4							InstanceGroupType=TASK,BidPrice=0.04,InstanceType=m3.xlarge,InstanceCount=2
```

## Use the Java SDK to Create an Instance Group<a name="emr-instance-group-sdk"></a>

You instantiate an `InstanceGroupConfig` object that specifies the configuration of an instance group for a cluster\. To use Spot Instances, you set the `withBidPrice` and `withMarket` properties on the `InstanceGroupConfig` object\. The following code shows how to define master, core, and task instance groups that run Spot Instances\.

```
 1. InstanceGroupConfig instanceGroupConfigMaster = new InstanceGroupConfig()
 2. 	.withInstanceCount(1)
 3. 	.withInstanceRole(“MASTER”)
 4. 	.withInstanceType(“m3.xlarge”)
 5. 	.withMarket("SPOT")
 6. 	.withBidPrice(“0.25”); 
 7. 	
 8. InstanceGroupConfig instanceGroupConfigCore = new InstanceGroupConfig()
 9. 	.withInstanceCount(4)
10. 	.withInstanceRole(“CORE”)
11. 	.withInstanceType(“m3.xlarge”)
12. 	.withMarket("SPOT")
13. 	.withBidPrice(“0.03”);
14. 	
15. InstanceGroupConfig instanceGroupConfigTask = new InstanceGroupConfig()
16. 	.withInstanceCount(2)
17. 	.withInstanceRole(“TASK”)
18. 	.withInstanceType(“m3.xlarge”)
19. 	.withMarket("SPOT")
20. 	.withBidPrice(“0.10”);
```