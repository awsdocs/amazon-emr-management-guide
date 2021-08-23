# Configure uniform instance groups<a name="emr-uniform-instance-group"></a>

With the instance groups configuration, each node type \(master, core, or task\) consists of the same instance type and the same purchasing option for instances: On\-Demand or Spot\. You specify these settings when you create an instance group\. They can't be changed later\. You can, however, add instances of the same type and purchasing option to core and task instance groups\. You can also remove instances\.

If the cluster's On\-Demand Instances match the attributes of open capacity reservations \(instance type, platform, tenancy and Availability Zone\) available in your account, the capacity reservations are applied automatically\. You can use open capacity reservations for master, core, and task nodes\. However, you cannot use targeted capacity reservations or prevent instances from launching into open capacity reservations with matching attributes when you provision clusters using instance groups\. If you want to use targeted capacity reservations or prevent instances from launching into open capacity reservations, use Instance Fleets instead\. For more information, see [Use capacity reservations with instance fleets](on-demand-capacity-reservations.md)\.

To add different instance types after a cluster is created, you can add additional task instance groups\. You can choose different instance types and purchasing options for each instance group\. For more information, see [Scaling cluster resources](emr-scale-on-demand.md)\.

When launching instances, the On\-Demand Instance's capacity reservation preference defaults to `open`, which enables it to run in any open capacity reservation that has matching attributes \(instance type, platform, Availability Zone\)\. For more information about On\-Demand Capacity Reservations, see [Use capacity reservations with instance fleets](on-demand-capacity-reservations.md)\.

This section covers creating a cluster with uniform instance groups\. For more information about modifying an existing instance group by adding or removing instances manually or with automatic scaling, see [Manage clusters](emr-manage.md)\.

## Use the console to configure uniform instance groups<a name="emr-uniform-instance-group-console"></a>

The following procedure covers **Advanced options** when you create a cluster\. Using **Quick options** also creates a cluster with the instance groups configuration\. For more information about using **Quick Options**, see the Getting Started tutorial\.

**To create a cluster with uniform instance groups using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**, enter **Software Configuration** options, and then choose **Next**\.

1. In the **Hardware Configuration** screen, leave **Uniform instance groups** selected\.

1. Choose the **Network**, and then choose the **EC2 Subnet** for your cluster\. The subnet that you choose is associated with an Availability Group, which is listed with each subnet\. For more information, see [Configure networking](emr-plan-vpc-subnet.md)\.
**Note**  
Your account and Region may give you the option to choose **Launch into EC2\-Classic** for **Network**\. If you choose that option, choose an **EC2 Availability Zone** rather than an **EC2 Subnet**\. For more information, see [Amazon EC2 and Amazon VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Within each **Node type** row:
   + Under **Node type**, if you want to change the default name of the instance group, choose the pencil icon and then enter a friendly name\. If want to remove the **Task** instance group, choose the X icon\. Choose **Add task instance group** to add additional **Task** instance groups\.
   + Under **Instance type**, choose the pencil icon and then choose the instance type you want to use for that node type\.
**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.
   + Under **Instance type**, choose the pencil icon for **Configurations**and then edit the configurations for applications for each instance group\.
   + Under **Instance count**, enter the number of instances to use for each node type\.
   + Under **Purchasing option, **choose **On\-Demand** or **Spot**\. If you choose **Spot**, select an option for the maximum price for Spot Instances\. By default, **Use On\-Demand as max price** is selected\. You can select **Set max $/hr** and then enter your maximum price\. Availability Zone of the **EC2 Subnet ** you chose is below the **Maximum Spot price**\.
**Tip**  
Pause on the information tooltip for **Spot** to see the current Spot price for Availability Zones in the current Region\. The lowest Spot price is in green\. You might want to use this information to change your **EC2 Subnet** selection\.
   + Under **Auto Scaling for Core and Task node types**, choose the pencil icon, and then configure the automatic scaling options\. For more information, see [Using automatic scaling with a custom policy for instance groups ](emr-automatic-scaling.md)\.

1. Choose **Add task instance group** as desired and configure settings as described in the previous step\.

1. Choose **Next**, modify other cluster settings, and then launch the cluster\.

## Use the AWS CLI to create a cluster with uniform instance groups<a name="emr-uniform-instance-group-cli"></a>

To specify the instance groups configuration for a cluster using the AWS CLI, use the `create-cluster` command along with the `--instance-groups` parameter\. Amazon EMR assumes the On\-Demand Instance option unless you specify the `BidPrice` argument for an instance group\. For examples of `create-cluster` commands that launch uniform instance groups with On\-Demand Instances and a variety of cluster options, type `aws emr create-cluster help `at the command line, or see [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html) in the *AWS CLI Command Reference*\.

You can use the AWS CLI to create uniform instance groups in a cluster that use Spot Instances\. The offered Spot price depends on Availability Zone\. When you use the CLI or API, you can specify the Availability Zone either with the `AvailabilityZone` argument \(if you're using an EC2\-classic network\) or the `SubnetID `argument of the `--ec2-attributes `parameter\. The Availability Zone or subnet that you select applies to the cluster, so it's used for all instance groups\. If you don't specify an Availability Zone or subnet explicitly, Amazon EMR selects the Availability Zone with the lowest Spot price when it launches the cluster\.

The following example demonstrates a `create-cluster` command that creates master, core, and two task instance groups that all use Spot Instances\. Replace *myKey* with the name of your EC2 key pair\. 

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

```
aws emr create-cluster --name "MySpotCluster" --release-label emr-5.33.0 \
--use-default-roles --ec2-attributes KeyName=myKey \
--instance-groups InstanceGroupType=MASTER,InstanceType=m5.xlarge,InstanceCount=1,BidPrice=0.25 \
InstanceGroupType=CORE,InstanceType=m5.xlarge,InstanceCount=2,BidPrice=0.03 \
InstanceGroupType=TASK,InstanceType=m5.xlarge,InstanceCount=4,BidPrice=0.03 \
InstanceGroupType=TASK,InstanceType=m5.xlarge,InstanceCount=2,BidPrice=0.04
```

Using the CLI, you can create uniform instance group clusters that specify a unique custom AMI for each instance type in the instance group\. This allows you to use different instance architectures in the same instance group\. Each instance type must use a custom AMI with a matching architecture\. For example, you would configure an m5\.xlarge instance type with an x86\_64 architecture custom AMI, and an m6g\.xlarge instance type with a corresponding `AWS AARCH64` \(ARM\) architecture custom AMI\. 

The following example shows a uniform instance group cluster created with two instance types, each with its own custom AMI\. Notice that the custom AMIs are specified only at the instance type level, not at the cluster level\. This is to avoid conflicts between the instance type AMIs and an AMI at the cluster level, which would cause the cluster launch to fail\. 

```
aws emr create-cluster --instance-groups 
InstanceGroupType=MASTER,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-123456 
InstanceGroupType=CORE,InstanceType=m6g.xlarge,InstanceCount=1,CustomAmiId=ami-234567
```

You can add multiple custom AMIs to an instance group that you add to a running cluster\. The `CustomAmiId` argument can be used with the `add-instance-groups` command as shown in the following example\.

```
aws emr add-instance-groups —cluster-id j-123456 —instance-groups InstanceGroupType=Task,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-123456
```

## Use the Java SDK to create an instance group<a name="emr-instance-group-sdk"></a>

You instantiate an `InstanceGroupConfig` object that specifies the configuration of an instance group for a cluster\. To use Spot Instances, you set the `withBidPrice` and `withMarket` properties on the `InstanceGroupConfig` object\. The following code shows how to define master, core, and task instance groups that run Spot Instances\.

```
InstanceGroupConfig instanceGroupConfigMaster = new InstanceGroupConfig()
	.withInstanceCount(1)
	.withInstanceRole("MASTER")
	.withInstanceType("m4.large")
	.withMarket("SPOT")
	.withBidPrice("0.25"); 
	
InstanceGroupConfig instanceGroupConfigCore = new InstanceGroupConfig()
	.withInstanceCount(4)
	.withInstanceRole("CORE")
	.withInstanceType("m4.large")
	.withMarket("SPOT")
	.withBidPrice("0.03");
	
InstanceGroupConfig instanceGroupConfigTask = new InstanceGroupConfig()
	.withInstanceCount(2)
	.withInstanceRole("TASK")
	.withInstanceType("m4.large")
	.withMarket("SPOT")
	.withBidPrice("0.10");
```