# Configure Instance Fleets<a name="emr-instance-fleet"></a>

The instance fleets configuration for a cluster offers the widest variety of provisioning options for EC2 instances\. You can specify up to five instance types per fleet, and those instance types can be provisioned using both On\-Demand and Spot purchasing options\. You can also select multiple Availability Zones, specify different maximum Spot prices for each instance, and choose additional Spot options for each instance fleet\. Amazon EMR uses the options you specify to more intelligently and quickly provision capacity when the cluster launches\. Also, while the cluster is running, if Amazon EC2 reclaims a Spot Instance because of a price increase, or an instance fails, Amazon EMR can try to replace the instances with any of the instance types that you specify\. This makes it easier to regain cluster capacity during a spike in Spot pricing\. You can develop a flexible and elastic resourcing strategy for each node type\. For example, within specific fleets, you can have a core of On\-Demand capacity supplemented with less\-expensive Spot capacity if available\. 

**Note**  
The instance fleets configuration is available only in Amazon EMR release versions 4\.8\.0 and later, excluding 5\.0\.0 and 5\.0\.3\.

## Instance Fleet Options<a name="emr-instance-fleet-options"></a>

### **Target Capacities and Weighted Capacity**<a name="emr-fleet-capacity"></a>

With each instance fleet, you establish a *target capacity* for On\-Demand Instances and a target capacity for Spot Instances\. Each of the five instance types available per instance fleet has a *weighted capacity* that you assign\. When you use the console, you can choose to have the vCPU of the instance type automatically assigned as the weighted capacity\. When using the CLI, you assign weighted capacities manually\.

**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

When Amazon EMR provisions a particular instance type, it can choose any mix of the five instance types you specify to fulfill these targets, and the weighted capacity of each provisioned instance counts toward the target capacity for On\-Demand or Spot as appropriate\. 

Amazon EMR provisions instances in a fleet until the target capacities are totally fulfilled, even if this results in an overage\. For example, if there are 2 units remaining to fulfill capacity, and Amazon EMR can only provision an instance with a weighted capacity of 5 units, the instance is provisioned nevertheless, and the target capacity is exceeded by 3 units\. 

### **Spot Instance Options**<a name="emr-fleet-spot-options"></a>

When you specify a target capacity for Spot Instances, you can specify a maximum Spot price for each of the five instance types\. Amazon EMR compares the maximum Spot price to Spot prices offered in the Availability Zones you select, provisioning Spot Instances if the current Spot price is below the maximum Spot price that you specify\.

<a name="emr-fleet-defined-duration"></a>For the fleet as a whole, when you specify a **Defined duration**, Spot Instances are not interrupted during the defined duration and terminate after it expires\. Defined duration pricing applies when you select this option\. For more information, see [Amazon EC2 Spot Instances Pricing](https://aws.amazon.com/ec2/spot/pricing/)\. If you don't specify a defined duration, instances terminate as soon as the Spot price exceeds the maximum Spot price\.

<a name="emr-fleet-spot-timeout"></a>For the fleet as a whole, you also define a **provisioning timeout** and the action to take when there is unfulfilled target capacity for Spot Instances when the timeout expires\. The timeout applies when the cluster is provisioning capacity when it is created\. You can have the cluster terminate or switch to provisioning On\-Demand capacity to fulfill the remaining Spot capacity\.

For more information about Spot Instances, see [Spot Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the Amazon EC2 User Guide for Linux Instances\.

### **Multiple Subnet \(Availability Zones\) Options**<a name="emr-multiple-subnet-options"></a>

You can specify multiple EC2 subnets within a VPC, each corresponding to a different Availability Zone\. \(If you use EC2\-Classic, you specify Availability Zones explicitly\.\) Amazon EMR hunts for the best fit from among those Availability Zones, and then provisions instances in the Availability Zone that offers the best fit\. Instances are always provisioned in only one Availability Zone\. You can select private subnets or public subnets, but you can't mix the two, and the subnets you specify must be within the same VPC\.

### **Master Node Configuration**<a name="emr-master-node-configuration"></a>

Because the master instance fleet is only a single instance, its configuration is slightly different from core and task instance fleets\. You only select either On\-Demand or Spot for the master instance fleet because it consists of only one instance\. If you use the console to create the instance fleet, the target capacity for the purchasing option you select is set to 1\. If you use the AWS CLI, always set either `TargetSpotCapacity` or `TargetOnDemandCapacity` to 1 as appropriate\. You can still choose up to five instance types for the master instance fleet\. However, unlike core and task instance fleets, where Amazon EMR might provision multiple instances of different types, Amazon EMR selects a single instance type to provision for the master instance fleet\.

### **Summary of Key Features**<a name="emr-key-feature-summary"></a>

+ One instance fleet, and only one, per node type \(master, core, task\)\. Up to five EC2 instance types specified for each fleet\. 

+ Amazon EMR chooses any or all of the five EC2 instance types to provision with both Spot and On\-Demand purchasing options\.

+ Establish target capacities for Spot and On\-Demand Instances per instance fleet\. Assign a weighted capacity to each instance type, which counts toward the target\. Amazon EMR provisions instances until each target capacity is totally fulfilled\.

+ Choose one subnet \(Availability Zone\) or a range\. Amazon EMR provisions capacity in the Availability Zone that is the best fit\. 

+ When you specify a target capacity for Spot Instances:

  + For each instance type, specify a maximum Spot price, either as a dollar amount or as percentage of the On\-Demand price\.

  + Specify a defined duration \(also known as a Spot block\) if desired\. Spot Instances terminate only after the defined duration expires\.

  + Define a timeout period for provisioning Spot Instances and the action to take if the timeout expires—terminate the cluster or switch to On\-Demand\.

## Use the Console to Configure Instance Fleets<a name="emr-instance-fleet-console"></a>

To create a cluster using instance fleets, use the **Advanced options** configuration in the Amazon EMR console\.

**To create a cluster with instance fleets using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**, enter **Software Configuration** options, and then choose **Next**\.

1. Choose **Instance fleets**\.

1. For **Network**, enter a value\. If you choose a VPC for **Network**, choose a single **EC2 Subnet** or CTRL \+ click to choose multiple EC2 subnets\. The subnets you select must be the same type \(public or private\)\. If you choose only one, your cluster launches in that subnet\. If you choose a group, the subnet with the best fit is selected from the group when the cluster launches\. 
**Note**  
Your account and region may give you the option to choose **Launch into EC2\-Classic** for **Network**\. If you choose that option, choose one or more from **EC2 Availability Zones** rather than **EC2 Subnets**\. For more information, see [Amazon EC2 and Amazon VPC](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Within each node type row, under **Node type**, if you want to change the default name of an instance fleet, click the pencil icon and then enter a friendly name\. If want to remove the **Task** instance fleet, click the X icon\.

1. Under **Target capacity**, choose options according to the following guidelines:

   + Choose how you want to define the **Target capacity**\. If you choose **vCPU**, the number of YARN vcores for each **Fleet instance type** is used as its weighted capacity\. If you choose **Generic units**, you assign a custom number for each target capacity, and then assign a custom weighted capacity to each instance type\. A field for this purpose appears for each instance you add under **Fleet instance type**\.

   + For the **Master** node, select whether the instance is **On\-demand** or **Spot**\.

   + For the **Core** and **Task** nodes, enter target capacities for **On\-demand** and **Spot**\. Amazon EMR provisions the **Fleet instance types** that you specify until these capacities are fulfilled\.

1. Under **Fleet instance types** for each **Node type**, choose options according to the following guidelines:

   + Choose **Add/remove instance types to fleet**, and then choose up to five instance types from the list\. Amazon EMR may choose to provision any mix of these instance types when it launches the cluster\.

   + If a Node type is configured with a **Target capacity** for **Spot**, choose **Maximum Spot price** options\. You can enter your maximum Spot price as a **% of On\-Demand** pricing, or you can enter a **Dollars \($\)** amount in USD\.
**Tip**  
Hover over the information tooltip for **Maximum Spot price** to see the Spot price for all Availability Zones in the current region\. The lowest Spot price is in green\. You can use this information to inform your **EC2 Subnet** selection\.

   + If you chose **Default units** for **Target capacity**, enter the weighted capacity you want to assign to each instance type in the **Each instance counts as** box\.

   + To have EBS volumes attached to the instance type when it's provisioned, click the pencil next to **EBS Storage ** and then enter EBS configuration options\.

1. If you established a **Target capacity** for **Spot**, choose **Advanced Spot options** according to the following guidelines:

   + **Defined duration**—if left to the default, **Not set**, Spot Instances terminate as soon as the Spot price rises above the Maximum Spot price, or when the cluster terminates\. If you set a value, Spot Instances don't terminate until the duration has expired\.
**Important**  
If you set a **Defined duration**, special defined duration pricing applies\. For pricing details, see [Amazon EC2 Spot Instances Pricing](https://aws.amazon.com/ec2/spot/pricing/)\.

   + **Provisioning timeout**—Use these settings to control what Amazon EMR does when it can't provision Spot Instances from among the **Fleet instance types** you specify\. You enter a timeout period in minutes, and then choose whether to **Terminate the cluster** or **Switch to provisioning On\-Demand Instances**\. If you choose to switch to On\-Demand Instances, the weighted capacity of On\-Demand Instances counts toward the target capacity for Spot Instances, and Amazon EMR provisions On\-Demand Instances until the target capacity for Spot Instances is fulfilled\.

1. Choose **Next**, modify other cluster settings, and then launch the cluster\.

## Use the CLI to Configure Instance Fleets<a name="emr-instance-fleet-cli"></a>

+ To create and launch a cluster with instance fleets, use the `create-cluster` command along with `--instance-fleet` parameters\.

+ To get configuration details of the instance fleets in a cluster, use the `list-instance-fleets` command\.

+ To make changes to the target capacity for an instance fleet, use the `modify-instance-fleet` command\.

+ To add a task instance fleet to a cluster that doesn't already have one, use the `add-instance-fleet` command\.

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

### Create a Cluster with the Instance Fleets Configuration<a name="create-cluster-instance-fleet-cli"></a>

The following examples demonstrate `create-cluster` commands with a variety of options that you can combine\.

**Note**  
If you have not previously created the default EMR service role and EC2 instance profile, use `aws emr create-default-roles` to create them before using the `create-cluster` command\.

**Example Example: On\-Demand Master, On\-Demand Core with Single Instance Type, Default VPC**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge}']
```

**Example Example: Spot Master, Spot Core with Single Instance Type, Default VPC**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5}'] \
InstanceFleetType=CORE,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5}']
```

**Example Example: On\-Demand Master, Mixed Core with Single Instance Type, Single EC2 Subnet**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=2,TargetSpotCapacity=6,InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5,WeightedCapacity=2}']
```

**Example Example: On\-Demand Master, Spot Core with Multiple Weighted Instance Types, Defined Duration and Timeout for Spot, Range of EC2 Subnets**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c','subnet-de67890f'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge}'] \
InstanceFleetType=CORE,TargetSpotCapacity=11,InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5,WeightedCapacity=3}',\
'{InstanceType=m4.2xlarge,BidPrice=0.9,WeightedCapacity=5}'],\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=120,TimeoutAction=SWITCH_TO_ON_DEMAND}'}
```

**Example Example: On\-Demand Master, Mixed Core and Task with Multiple Weighted Instance Types, Defined Duration and Timeout for Core Spot Instances, Range of EC2 Subnets**  

```
aws emr create-cluster --release-label emr-5.3.1 --service-role EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,SubnetIds=['subnet-ab12345c','subnet-de67890f'] \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m3.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=8,TargetSpotCapacity=6,\
InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5,WeightedCapacity=3}',\
'{InstanceType=m4.2xlarge,BidPrice=0.9,WeightedCapacity=5}'],\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=120,TimeoutAction=SWITCH_TO_ON_DEMAND}'} \
InstanceFleetType=TASK,TargetOnDemandCapacity=3,TargetSpotCapacity=3,\
InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5,WeightedCapacity=3}']
```

**Example Example: Spot Master, No Core or Task, EBS Configuration, Default VPC**  

```
aws emr create-cluster --release-label emr 5.3.1 -service-role EMR_DefaultRole \ 
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetSpotCapacity=1,\
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=60,TimeoutAction=TERMINATE_CLUSTER}'},\
InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5,\
EbsConfiguration={EbsOptimized=true,EbsBlockDeviceConfigs=[{VolumeSpecification={VolumeType=gp2,\
SizeIn GB=100}},{VolumeSpecification={VolumeType=io1,SizeInGB=100,Iop s=100},VolumesPerInstance=4}]}}']
```

**Example Use a JSON Configuration File**  
You can configure instance fleet parameters in a JSON file, and then reference the JSON file as the sole parameter for instance fleets\. For example, the following command references a JSON configuration file, my\-fleet\-config\.json:  

```
aws emr create-cluster --release-label emr-5.2.0 --servicerole EMR_DefaultRole \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets file://my-fleet-config.json
```
The my\-fleet\-config\.json specifies master, core, and task instance fleets as shown in the following example\. The core instance fleet uses a maximum Spot price \(`BidPrice`\) as a percentage of on\-demand, while the task and master instance fleets use a maximum Spot price \(BidPriceAsPercentageofOnDemandPrice\) as a string in USD\.  

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
                "InstanceType": "m3.xlarge",
                "BidPrice": "0.89"
            }
        ]
    },
    {
        "Name": "Corefleet",
        "InstanceFleetType": "CORE",
        "TargetSpotCapacity": 1,
        "LaunchSpecifications": {
            "SpotSpecification": {
                "TimeoutDurationMinutes": 120,
                "TimeoutAction": "TERMINATE_CLUSTER"
            }
        },
        "InstanceTypeConfigs": [
            {
                "InstanceType": "m3.xlarge",
                "BidPriceAsPercentageOfOnDemandPrice": 100
            }
        ]
    },
    {
        "Name": "Taskfleet",
        "InstanceFleetType": "TASK",
        "TargetSpotCapacity": 1,
        "LaunchSpecifications": {
            "SpotSpecification": {
                "TimeoutDurationMinutes": 120,
                "TimeoutAction": "TERMINATE_CLUSTER"
            }
        },
        "InstanceTypeConfigs": [
            {
                "InstanceType": "m3.xlarge",
                "BidPrice": "0.89"
            }
        ]
    }
]
```

### Get Configuration Details of Instance Fleets in a Cluster<a name="w3ab1c18c29c25c14c10b8"></a>

Use the `list-instance-fleets` command to get configuration details of the instance fleets in a cluster\. The command takes a cluster ID as input\. The following example demonstrates the command and its output for a cluster that contains a master task instance group and a core task instance group\. For full response syntax, see [ListInstanceFleets](http://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_ListInstanceFleets.html) in the *Amazon EMR API Reference\.*

```
list-instance-fleets --cluster-id 'j-12ABCDEFGHI34JK'
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
                    "InstanceType": "m3.xlarge",
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
                    "InstanceType": "m3.xlarge",
                    "WeightedCapacity": 1
                }
            ],
           "Id": "if-2ABC4DEFGHIJ4"
        }
    ]
}
```

### Modify Target Capacities for an Instance Fleet<a name="emr-fleet-modify-target-cli"></a>

Use the `modify-instance-fleet` command to specify new target capacities for an instance fleet\. You must specify the cluster ID and the instance fleet ID\. Use the `list-instance-fleets` command to retrieve instance fleet IDs\.

```
aws emr modify-instance-fleet --cluster-id 'j-12ABCDEFGHI34JK' /
--instance-fleet InstanceFleetId='if-2ABC4DEFGHIJ4',TargetOnDemandCapacity=1,TargetSpotCapacity=1
```

### Add a Task Instance Fleet to a Cluster<a name="emr-task-instance-fleet"></a>

If a cluster has only master and core instance fleets, you can use the `add-instance-fleet` command to add a task instance fleet\. You can only use this to add task instance fleets\.

```
aws emr add-instance-fleet --cluster-id 'j-12ABCDEFGHI34JK' --instance-fleet  InstanceFleetType=TASK,TargetSpotCapacity=1,/
LaunchSpecifications={SpotSpecification='{TimeoutDurationMinutes=20,TimeoutAction=TERMINATE_CLUSTER}'},/
InstanceTypeConfigs=['{InstanceType=m3.xlarge,BidPrice=0.5}']
```