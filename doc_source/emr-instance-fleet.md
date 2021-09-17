# Configure instance fleets<a name="emr-instance-fleet"></a>

**Note**  
The instance fleets configuration is available only in Amazon EMR release versions 4\.8\.0 and later, excluding 5\.0\.0 and 5\.0\.3\.

The instance fleet configuration for Amazon EMR clusters lets you select a wide variety of provisioning options for Amazon EC2 instances, and helps you develop a flexible and elastic resourcing strategy for each node type in your cluster\. 

In an instance fleet configuration, you specify a *target capacity* for [On\-Demand Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html) and [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) within each fleet\. When the cluster launches, Amazon EMR provisions instances until the targets are fulfilled\. When Amazon EC2 reclaims a Spot Instance in a running cluster because of a price increase or instance failure, Amazon EMR tries to replace the instance with any of the instance types that you specify\. This makes it easier to regain capacity during a spike in Spot pricing\. 

You can specify a maximum of five Amazon EC2 instance types per fleet for Amazon EMR to use when fulfilling the targets, or a maximum of 30 Amazon EC2 instance types per fleet when you create a cluster using the AWS CLI or Amazon EMR API and an [allocation strategy](#emr-instance-fleet-allocation-strategy) for On\-Demand and Spot Instances\. 

You can also select multiple subnets for different Availability Zones\. When Amazon EMR launches the cluster, it looks across those subnets to find the instances and purchasing options you specify\. If Amazon EMR detects an AWS large\-scale event in one or more of the Availability Zones, Amazon EMR automatically attempts to route traffic away from the impacted Availability Zones and tries to launch clusters in alternate Availability Zones according to your selections\.

## **Considerations**<a name="emr-key-feature-summary"></a>

Consider the following items when you use instance fleets with Amazon EMR\.
+ You can have one instance fleet, and only one, per node type \(master, core, task\)\. You can specify up to five Amazon EC2 instance types specified for each fleet \(or a maximum of 30 types per instance fleet when you create a cluster using the AWS CLI or Amazon EMR API and an [Allocation strategy for instance fleets](#emr-instance-fleet-allocation-strategy)\)\. 
+ Amazon EMR chooses any or all of the specified Amazon EC2 instance types to provision with both Spot and On\-Demand purchasing options\.
+ You can establish target capacities for Spot and On\-Demand Instances for the core fleet and task fleet\. Use vCPU or a generic unit assigned to each Amazon EC2 instance that counts toward the targets\. Amazon EMR provisions instances until each target capacity is totally fulfilled\. For the master fleet, the target is always one\.
+ You can choose one subnet \(Availability Zone\) or a range\. If you choose a range, Amazon EMR provisions capacity in the Availability Zone that is the best fit\.
+ When you specify a target capacity for Spot Instances:
  + For each instance type, specify a maximum Spot price\. Amazon EMR provisions Spot Instances if the Spot price is below the maximum Spot price\. You pay the Spot price, not necessarily the maximum Spot price\.
  + For each fleet, define a timeout period for provisioning Spot Instances\. If Amazon EMR can't provision Spot capacity, you can terminate the cluster or switch to provisioning On\-Demand capacity instead\.
+ For each fleet, you can choose to apply an allocation strategy \- lowest\-price for On\-Demand Instances; capacity\-optimized for Spot Instances\.
+ For each fleet with On\-Demand `allocation strategy - lowest-price`, you can choose to apply capacity reservation options\.
+ Check your subnet size before launching your cluster\. When you provision a cluster with a task fleet and there aren't enough IP addresses available in the corresponding subnet, the fleet will go into a suspended state instead of terminating the cluster with an error\. To avoid this issue, we recommend increasing the number of IP addresses in your subnets\.

## Instance fleet options<a name="emr-instance-fleet-options"></a>

Use the following guidelines to understand instance fleet options\.

### **Setting target capacities**<a name="emr-fleet-capacity"></a>

Specify the target capacities you want for the core fleet and task fleet\. When you do, that determines the number of On\-Demand Instances and Spot Instances that Amazon EMR provisions\. When you specify an instance, you decide how much each instance counts toward the target\. When an On\-Demand Instance is provisioned, it counts toward the On\-Demand target\. The same is true for Spot Instances\. Unlike core and task fleets, the master fleet is always one instance\. Therefore, the target capacity for this fleet is always one\. 

When you use the console, the vCPUs of the Amazon EC2 instance type are used as the count for target capacities by default\. You can change this to **Generic units**, and then specify the count for each EC2 instance type\. When you use the AWS CLI, you manually assign generic units for each instance type\. 

**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For each fleet, you specify up to five Amazon EC2 instance types\. If you use an [Allocation strategy for instance fleets](#emr-instance-fleet-allocation-strategy) and create a cluster using the AWS CLI or the Amazon EMR API, you can specify up to 30 EC2 instance types per instance fleet\. Amazon EMR chooses any combination of these EC2 instance types to fulfill your target capacities\. Because Amazon EMR wants to fill target capacity completely, an overage might happen\. For example, if there are two unfulfilled units, and Amazon EMR can only provision an instance with a count of five units, the instance still gets provisioned, meaning that the target capacity is exceeded by three units\. 

If you reduce the target capacity to resize a running cluster, Amazon EMR attempts to complete application tasks and terminates instances to meet the new target\. For more information, see [Terminate at task completion](emr-scaledown-behavior.md#emr-scaledown-terminate-task)\. Amazon EMR has a 60\-minute timeout for completing a resize operation\. In some cases, a node may still have tasks running after 60 minutes, and Amazon EMR reports that the resize operation was successful and that the new target was not met\.

### **Launch options**<a name="emr-fleet-spot-options"></a>

For Spot Instances, you can specify a **Maximum Spot price** for each instance type in a fleet\. You can set this price either as a percentage of the On\-Demand price, or as a specific dollar amount\. Amazon EMR provisions Spot Instances if the current Spot price in an Availability Zone is below your maximum Spot price\. You pay the Spot price, not necessarily the maximum Spot price\.

**Note**  
Spot Instances with a defined duration \(also known as Spot blocks\) are no longer available to new customers from July 1, 2021\. For customers who have previously used the feature, we will continue to support Spot Instances with a defined duration until December 31, 2022\.

<a name="emr-fleet-spot-timeout"></a>For each fleet, you also define a **Provisioning timeout**\. The timeout applies when the cluster is provisioning capacity when it is created and can't provision enough Spot Instances to fulfill target capacity according to your specifications\. You specify the timeout period and the action to take\. You can have the cluster terminate or switch to provisioning On\-Demand capacity to fulfill the remaining Spot capacity\. When you choose to switch to On\-Demand, the remaining Spot capacity is effectively added to the On\-Demand target capacity after the timeout expires\.

Available in Amazon Amazon EMR 5\.12\.1 and later, you have the option to launch Spot and On\-Demand Instance fleets with optimized capacity allocation\. This allocation strategy option can be set in the AWS Management Console or using the API `RunJobFlow`\. Using the allocation strategy option requires additional service role permissions\. If you use the default Amazon EMR service role and managed policy \([EMR\_DefaultRole](emr-iam-role.md) and AmazonEMRServicePolicy\_v2\) for the cluster, the permissions for the allocation strategy option are already included\. If you're not using the default Amazon EMR service role and managed policy, you must add them to use this option\. See [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)\.

For more information about Spot Instances, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the Amazon EC2 User Guide for Linux Instances\. For more information about On\-Demand Instances, see [On\-Demand Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html) in the Amazon EC2 User Guide for Linux Instances\.

If you choose to launch On\-Demand Instance fleets with the lowest\-price allocation strategy, you have the option to use capacity reservations\. Capacity reservation options can be set using the Amazon EMR API `RunJobFlow`\. Capacity reservations require additional service role permissions which you must add to use these options\. See [Required IAM permissions to use an allocation strategy](#create-cluster-allocation-policy)\.

### **Multiple subnet \(Availability Zones\) options**<a name="emr-multiple-subnet-options"></a>

When you use instance fleets, you can specify multiple Amazon EC2 subnets within a VPC, each corresponding to a different Availability Zone\. If you use EC2\-Classic, you specify Availability Zones explicitly\. Amazon EMR identifies the best Availability Zone to launch instances according to your fleet specifications\. Instances are always provisioned in only one Availability Zone\. You can select private subnets or public subnets, but you can't mix the two, and the subnets you specify must be within the same VPC\.

### **Master node configuration**<a name="emr-master-node-configuration"></a>

Because the master instance fleet is only a single instance, its configuration is slightly different from core and task instance fleets\. You only select either On\-Demand or Spot for the master instance fleet because it consists of only one instance\. If you use the console to create the instance fleet, the target capacity for the purchasing option you select is set to 1\. If you use the AWS CLI, always set either `TargetSpotCapacity` or `TargetOnDemandCapacity` to 1 as appropriate\. You can still choose up to five instance types for the master instance fleet \(or a maximum of 30 when you use the allocation strategy option for On\-Demand or Spot Instances\)\. However, unlike core and task instance fleets, where Amazon EMR might provision multiple instances of different types, Amazon EMR selects a single instance type to provision for the master instance fleet\.

## Allocation strategy for instance fleets<a name="emr-instance-fleet-allocation-strategy"></a>

With Amazon EMR versions 5\.12\.1 and later, you can use the allocation strategy option with On\-Demand and Spot Instances for each cluster node\. When you create a cluster using the AWS CLI or Amazon EMR API and an allocation strategy option, you can specify a maximum of 30 Amazon EC2 instance types per fleet, as opposed to the five allowed when you use the default Amazon EMR cluster instance fleet configuration\. We recommend that you use the allocation strategy option for faster cluster provisioning, more accurate Spot Instance allocation, and fewer Spot Instance interruptions\.
+ **On\-Demand Instances** use a lowest\-price strategy, which launches the lowest\-priced instances first\. When you launch On\-Demand Instances, you have the option to use open or targeted capacity reservations in your accounts\. You can use open capacity reservations for master, core and task nodes\. You may experience insufficient capacity when using On\-Demand Instances with allocation strategy for instance fleets\. We recommend specifying a larger number of instance types to diversify and reduce the chance of experiencing insufficient capacity\. For more information, see [Use capacity reservations with instance fleets](on-demand-capacity-reservations.md)\.
+ **Spot Instances** use a capacity\-optimized strategy, which launches Spot Instances from Spot Instance pools that have optimal capacity for the number of instances that are launching\.

Using the allocation strategy option requires several IAM permissions that are automatically included in the default Amazon EMR service role and Amazon EMR managed policy \(EMR\_DefaultRole and AmazonEMRServicePolicy\_v2\)\. If you use a custom service role or managed policy for your cluster, you must add these permissions before you create the cluster\. For more information, see [Required IAM permissions to use an allocation strategy](#create-cluster-allocation-policy)\.

Optional On\-Demand Capacity Reservations \(ODCRs\) are available when you use the On\-Demand allocation strategy option\. Capacity reservation options let you specify a preference for using reserved capacity first for Amazon EMR clusters\. You can use this to ensure that your critical workloads use the capacity you have already reserved using open or targeted ODCRs\. For non\-critical workloads, the capacity reservation preferences let you specify whether reserved capacity should be consumed\.

Capacity reservations can only be used by instances that match their attributes \(instance type, platform, and Availability Zone\)\. By default, open capacity reservations are automatically used by Amazon EMR when provisioning On\-Demand Instances that match the instance attributes\. If you don't have any running instances that match the attributes of the capacity reservations, they remain unused until you launch an instance matching their attributes\. If you don't want to use any capacity reservations when launching your cluster, you must set capacity reservation preference to **none** in launch options\.

However, you can also target a capacity reservation for specific workflows\. This enables you to explicitly control which instances are allowed to run in that reserved capacity\. For more information about On\-Demand Capacity Reservations, see [Use capacity reservations with instance fleets](on-demand-capacity-reservations.md)\.

## Required IAM permissions to use an allocation strategy<a name="create-cluster-allocation-policy"></a>

Your [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md) requires additional permissions to create a cluster that uses the allocation strategy option for On\-Demand or Spot Instance fleets\.

The required permissions are automatically included in the default Amazon EMR service role and Amazon EMR managed policy \([EMR\_DefaultRole](emr-iam-role.md) and AmazonEMRServicePolicy\_v2\)\. If you use a custom service role or managed policy for your cluster, you must add the following permissions\.

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ec2:DeleteLaunchTemplate",
				"ec2:CreateLaunchTemplate",
				"ec2:DescribeLaunchTemplates",
				"ec2:CreateLaunchTemplateVersion",
				"ec2:CreateFleet"
			],
			"Resource": "*"
		}
}
```

Following are the service role permissions required to create a cluster that uses open or targeted capacity reservations\. You must include these permissions in addition to the permissions required for using the allocation strategy option\.

**Example Policy document for service role capacity reservations**  
To use open capacity reservations, you must include the following additional permissions\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeCapacityReservations",
                "ec2:DescribeLaunchTemplateVersions",
                "ec2:DeleteLaunchTemplateVersions"
            ],
            "Resource": "*"
        }
    ]
}
```

**Example**  
To use targeted capacity reservations, you must include the following additional permissions\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeCapacityReservations",
                "ec2:DescribeLaunchTemplateVersions",
                "ec2:DeleteLaunchTemplateVersions",
                "resource-groups:ListGroupResources"
            ],
            "Resource": "*"
        }
    ]
}
```

## Use the console to configure instance fleets<a name="emr-instance-fleet-console"></a>

To create a cluster using instance fleets, use the **Advanced options** configuration in the Amazon EMR console\.

**To create a cluster with instance fleets using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. At the top of the console window, choose **Go to advanced options**, enter **Software Configuration** options, and then choose **Next**\.

1. Under **Cluster Composition**, choose **Instance fleets**\. When you select the instance fleets option, you should see options to specify the **Target capacity** of On\-demand and Spot Instances appear in the **Cluster Nodes and Instances** table\.

1. For **Network**, enter a value\. If you choose a VPC for **Network**, choose a single **EC2 Subnet** or CTRL \+ click to choose multiple Amazon EC2 subnets\. The subnets you select must be the same type \(public or private\)\. If you choose only one, your cluster launches in that subnet\. If you choose a group, the subnet with the best fit is selected from the group when the cluster launches\. 
**Note**  
Your account and Region may give you the option to choose **Launch into EC2\-Classic** for **Network**\. If you choose that option, choose one or more from **EC2 Availability Zones** rather than **EC2 Subnets**\. For more information, see [Amazon EC2 and Amazon VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Under **Allocation Strategy**, select the check box to apply allocation strategies\.

   If you don't want to use the new allocation strategy option, leave the check box unchecked\.

1. For each **Node type**, if you want to change the default name of an instance fleet, choose the pencil icon and then enter a friendly name\. If want to remove the **Task** instance fleet, choose the X icon on the right side of the Task row\.

1. Choose **Add/remove instance types to fleet** and choose up to five instance types from the list for master and core instance fleets; add up to fifteen instance types for task instance fleets\. Amazon EMR may choose to provision any mix of these instance types when it launches the cluster\.

1. For each core and task instance type, choose how you want to define the weighted capacity \(**Each instance counts as X units**\) for that instance\. The number of YARN vCores for each Fleet instance type is used as the default weighted capacity units, but you can change the value to any units that make sense for your applications\.

1. Under **Target capacity**, define the total number of On\-Demand and Spot Instances you want per fleet\. EMR ensures that instances in the fleet fulfill the requested units for On\-Demand and Spot target capacity\. If no On\-Demand or Spot units are specified for a fleet, then no capacity is provisioned for that fleet\.

1. If a fleet is configured with a Target capacity for Spot, you can enter your maximum Spot price as a % of On\-Demand pricing, or you can enter a Dollars \($\) amount in USD\.

1. To have EBS volumes attached to the instance type when it's provisioned, choose the pencil next to EBS Storage and then enter EBS configuration options\.

1. If you established an instant count for **Spot units**, set **Advanced Spot options** according to the following guidelines:
   + **Provisioning timeout**—Use these settings to control what Amazon EMR does when it can't provision Spot Instances from among the **Fleet instance types** you specify\. You enter a timeout period in minutes, and then choose whether to **Terminate the cluster** or **Switch to provisioning On\-Demand Instances**\. If you choose to switch to On\-Demand Instances, the assigned capacity of On\-Demand Instances counts toward the target capacity for Spot Instances, and Amazon EMR provisions On\-Demand Instances until the target capacity for Spot Instances is fulfilled\.

1. Choose **Next**, modify other cluster settings, and then choose **Next**\.

1. If you selected to apply the new allocation strategy option, in the **Security Options** settings, select an **EMR role** and **EC2 instance profile** that contain the permissions required for the allocation strategy option\. Otherwise, the cluster creation will fail\.

1. Choose **Create Cluster**\.

## Use the CLI to configure instance fleets<a name="emr-instance-fleet-cli"></a>
+ To create and launch a cluster with instance fleets, use the `create-cluster` command along with `--instance-fleet` parameters\.
+ To get configuration details about the instance fleets in a cluster, use the `list-instance-fleets` command\.
+ To add multiple custom Amazon Linux AMIs to a cluster you’re creating, use the `CustomAmiId` option with each `InstanceType` specification\. You can configure instance fleet nodes with multiple instance types and multiple custom AMIs to fit your requirements\. See [Create a cluster with the instance fleets configuration](#create-cluster-instance-fleet-cli)\. 
+ To make changes to the target capacity for an instance fleet, use the `modify-instance-fleet` command\.
+ To add a task instance fleet to a cluster that doesn't already have one, use the `add-instance-fleet` command\.
+ Multiple custom AMIs can be added to the task instance fleet using the CustomAmiId argument with the add\-instance\-fleet command\. See [Create a cluster with the instance fleets configuration](#create-cluster-instance-fleet-cli)\.
+ To use the allocation strategy option when creating an instance fleet, update the service role to include the example policy document in the following section\.
+ To use the capacity reservations options when creating an instance fleet with On\-Demand allocation strategy, update the service role to include the example policy document in the following section\.
+ The instance fleets are automatically included in the default EMR service role and Amazon EMR managed policy \(EMR\_DefaultRole and AmazonEMRServicePolicy\_v2\)\. If you are using a custom service role or custom managed policy for your cluster, you must add the new permissions for allocation strategy in the following section\.

## Create a cluster with the instance fleets configuration<a name="create-cluster-instance-fleet-cli"></a>

The following examples demonstrate `create-cluster` commands with a variety of options that you can combine\.

**Note**  
If you have not previously created the default Amazon EMR service role and EC2 instance profile, use `aws Amazon EMR create-default-roles` to create them before using the `create-cluster` command\.

**Example: On\-Demand master, On\-Demand core with single instance type, Default VPC**  

```
aws Amazon EMR create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge}']
```

**Example: Spot master, Spot core with single instance type, default VPC**  

```
aws Amazon EMR create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5}'] \
InstanceFleetType=CORE,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5}']
```

**Example: On\-Demand master, mixed core with single instance type, single EC2 subnet**  

```
aws Amazon EMR create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=2,TargetSpotCapacity=6,InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5,WeightedCapacity=2}']
```

**Example: On\-Demand master, spot core with multiple weighted instance Types, Timeout for Spot, Range of EC2 Subnets**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c','subnet-de67890f'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge}'] \
InstanceFleetType=CORE,TargetSpotCapacity=11,InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5,WeightedCapacity=3}',\
'{InstanceType=m4.2xlarge,BidPrice=0.9,WeightedCapacity=5}'],\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=120,TimeoutAction=SWITCH_TO_ON_DEMAND}'}
```

**Example: On\-Demand master, mixed core and task with multiple weighted instance types, timeout for core Spot Instances, range of EC2 subnets**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c','subnet-de67890f'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=8,TargetSpotCapacity=6,\
InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5,WeightedCapacity=3}',\
'{InstanceType=m4.2xlarge,BidPrice=0.9,WeightedCapacity=5}'],\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=120,TimeoutAction=SWITCH_TO_ON_DEMAND}'} \
InstanceFleetType=TASK,TargetOnDemandCapacity=3,TargetSpotCapacity=3,\
InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5,WeightedCapacity=3}']
```

**Example: Spot master, no core or task, Amazon EBS configuration, default VPC**  

```
aws Amazon EMR create-cluster --release-label Amazon EMR 5.3.1 -service-role EMR_DefaultRole \ 
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetSpotCapacity=1,\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=60,TimeoutAction=TERMINATE_CLUSTER}'},\
InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5,\
EbsConfiguration={EbsOptimized=true,EbsBlockDeviceConfigs=[{VolumeSpecification={VolumeType=gp2,\
SizeIn GB=100}},{VolumeSpecification={VolumeType=io1,SizeInGB=100,Iop s=100},VolumesPerInstance=4}]}}']
```

**Example Multiple custom AMIs, multiple instance types, on\-demand master, on\-demand core**  

```
aws Amazon EMR create-cluster —instance-fleets 
InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge, CustomAmiId=ami-123456},{InstanceType=m6g.xlarge, CustomAmiId=ami-234567}'] 
InstanceFleetType=CORE,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-123456},{InstanceType=m6g.xlarge, CustomAmiId=ami-234567}']
```

**Example Add a task node to a running cluster with multiple instance types and multiple custom AMIs**  

```
aws Amazon EMR add-instance-fleet —cluster-id j-123456 —instance-fleet 
InstanceFleetType=Task,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-123456}', '{InstanceType=m6g.xlarge,CustomAmiId=ami-234567}']
```

**Example Use a JSON configuration file**  
You can configure instance fleet parameters in a JSON file, and then reference the JSON file as the sole parameter for instance fleets\. For example, the following command references a JSON configuration file, `my-fleet-config.json`:  

```
aws Amazon EMR create-cluster --release-label emr-5.30.0 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets file://my-fleet-config.json
```
The *my\-fleet\-config\.json* file specifies master, core, and task instance fleets as shown in the following example\. The core instance fleet uses a maximum Spot price \(`BidPrice`\) as a percentage of On\-Demand, while the task and master instance fleets use a maximum Spot price \(BidPriceAsPercentageofOnDemandPrice\) as a string in USD\.  

```
[
    {
        "Name": "Masterfleet",
        "InstanceFleetType": "MASTER",
        "TargetSpotCapacity": 1,
        "LaunchSpecifications": {
            "SpotSpecification": {
                "TimeoutDurationMinutes": 120,
                "TimeoutAction": "SWITCH_TO_ON_DEMAND"
            }
        },
        "InstanceTypeConfigs": [
            {
                "InstanceType": "m5.xlarge",
                "BidPrice": "0.89"
            }
        ]
    },
    {
        "Name": "Corefleet",
        "InstanceFleetType": "CORE",
        "TargetSpotCapacity": 1,
        "TargetOnDemandCapacity": 1,
        "LaunchSpecifications": {
          "OnDemandSpecification": {
            "AllocationStrategy": "lowest-price",
            "CapacityReservationOptions": 
            {
                "UsageStrategy": "use-capacity-reservations-first",
                "CapacityReservationResourceGroupArn": "String"
            }
        },
            "SpotSpecification": {
                "AllocationStrategy": "capacity-optimized",
                "TimeoutDurationMinutes": 120,
                "TimeoutAction": "TERMINATE_CLUSTER"
            }
        },
        "InstanceTypeConfigs": [
            {
                "InstanceType": "m5.xlarge",
                "BidPriceAsPercentageOfOnDemandPrice": 100
            }
        ]
    },
    {
        "Name": "Taskfleet",
        "InstanceFleetType": "TASK",
        "TargetSpotCapacity": 1,
        "LaunchSpecifications": {
          "OnDemandSpecification": {
            "AllocationStrategy": "lowest-price",
            "CapacityReservationOptions": 
            {
                "CapacityReservationPreference": "none"
            }
        },
            "SpotSpecification": {
                "TimeoutDurationMinutes": 120,
                "TimeoutAction": "TERMINATE_CLUSTER"
            }
        },
        "InstanceTypeConfigs": [
            {
                "InstanceType": "m5.xlarge",
                "BidPrice": "0.89"
            }
        ]
    }
]
```

## Modify target capacities for an instance fleet<a name="emr-fleet-modify-target-cli"></a>

Use the `modify-instance-fleet` command to specify new target capacities for an instance fleet\. You must specify the cluster ID and the instance fleet ID\. Use the `list-instance-fleets` command to retrieve instance fleet IDs\.

```
aws Amazon EMR modify-instance-fleet --cluster-id <cluster-id> /
--instance-fleet InstanceFleetId='<instance-fleet-id>',TargetOnDemandCapacity=1,TargetSpotCapacity=1
```

## Add a task instance fleet to a cluster<a name="emr-task-instance-fleet"></a>

If a cluster has only master and core instance fleets, you can use the `add-instance-fleet` command to add a task instance fleet\. You can only use this to add task instance fleets\.

```
aws Amazon EMR add-instance-fleet --cluster-id <cluster-id> --instance-fleet  InstanceFleetType=TASK,TargetSpotCapacity=1,/
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=20,TimeoutAction=TERMINATE_CLUSTER}'},/
InstanceTypeConfigs=['{InstanceType=m5.xlarge,BidPrice=0.5}']
```

## Get configuration details of instance fleets in a cluster<a name="emr-instance-fleet-get-configuration"></a>

Use the `list-instance-fleets` command to get configuration details of the instance fleets in a cluster\. The command takes a cluster ID as input\. The following example demonstrates the command and its output for a cluster that contains a master task instance group and a core task instance group\. For full response syntax, see [ListInstanceFleets](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_ListInstanceFleets.html) in the *Amazon EMR API Reference\.*

```
list-instance-fleets --cluster-id <cluster-id>			
```

```
{
    "InstanceFleets": [
        {
            "Status": {
                "Timeline": {
                    "ReadyDateTime": 1488759094.637,
                    "CreationDateTime": 1488758719.817
                },
                "State": "RUNNING",
                "StateChangeReason": {
                    "Message": ""
                }
            },
            "ProvisionedSpotCapacity": 6,
            "Name": "CORE",
            "InstanceFleetType": "CORE",
            "LaunchSpecifications": {
                "SpotSpecification": {
                    "TimeoutDurationMinutes": 60,
                    "TimeoutAction": "TERMINATE_CLUSTER"
                }
            },
            "ProvisionedOnDemandCapacity": 2,
            "InstanceTypeSpecifications": [
                {
                    "BidPrice": "0.5",
                    "InstanceType": "m5.xlarge",
                    "WeightedCapacity": 2
                }
            ],
            "Id": "if-1ABC2DEFGHIJ3"
        },
        {
            "Status": {
                "Timeline": {
                    "ReadyDateTime": 1488759058.598,
                    "CreationDateTime": 1488758719.811
                },
                "State": "RUNNING",
                "StateChangeReason": {
                    "Message": ""
                }
            },
            "ProvisionedSpotCapacity": 0,
            "Name": "MASTER",
            "InstanceFleetType": "MASTER",
            "ProvisionedOnDemandCapacity": 1,
            "InstanceTypeSpecifications": [
                {
                    "BidPriceAsPercentageOfOnDemandPrice": 100.0,
                    "InstanceType": "m5.xlarge",
                    "WeightedCapacity": 1
                }
            ],
           "Id": "if-2ABC4DEFGHIJ4"
        }
    ]
}
```