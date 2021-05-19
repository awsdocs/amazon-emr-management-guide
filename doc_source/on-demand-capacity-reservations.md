# Use Capacity Reservations with Instance Fleets<a name="on-demand-capacity-reservations"></a>

To launch On\-Demand instance fleets with Capacity Reservation options, attach additional service role permissions which are required to use Capacity Reservation options\. Since Capacity Reservation options must be used together with On\-Demand allocation strategy, you also have to include the permissions required for allocation strategy in your service role and managed policy\. For more information, see [Example policy document for service role](emr-instance-fleet.md#create-cluster-allocation-policy)\.

Amazon EMR supports both open and targeted Capacity Reservations\. The following topics show instance fleets configurations that you can use with the `RunJobFlow` action or `create-cluster` command to launch instance fleets using On\-Demand Capacity Reservations\. 

## Use Open Capacity Reservations on a best\-effort basis<a name="on-demand-capacity-reservations-best-effort"></a>

If the cluster’s On\-Demand instances match the attributes of open Capacity Reservations \(instance type, platform, tenancy and Availability Zone\) available in your account, the Capacity Reservations are applied automatically\. However, it is not guaranteed that your Capacity Reservations will be used\. For provisioning the cluster, Amazon EMR evaluates all the instance pools specified in the launch request and uses the one with the lowest\-price that has sufficient capacity to launch all the requested core nodes\. Available open Capacity Reservations that match the instance pool are applied automatically\. If available open Capacity Reservations do not match the instance pool, they remain unused\.

Once the core nodes are provisioned, the availability zone is selected and fixed\. Amazon EMR provisions task nodes into instance pools, starting with the lowest\-priced ones first, in the selected availability zone until all the task nodes are provisioned\. Available open Capacity Reservations that match the instance pools are applied automatically\.

The following are use cases of Amazon EMR capacity allocation logic for using open Capacity Reservations on a best\-effort basis\.

**Example 1: Lowest\-price instance pool in launch request has available open Capacity Reservations**

In this case, Amazon EMR launches capacity in the lowest\-price instance pool with On\-Demand instances\. Your available open Capacity Reservations in that instance pool are used automatically\.


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Requested Capacity | 100 | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
| Available Open Capacity Reservations | 150 | 100 | 100 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | 100 | \- | \- | 
| --- |--- |--- |--- |
| Open Capacity Reservation Used | 100 | \- | \- | 
| --- |--- |--- |--- |
| Available Open Capacity Reservations | 50 | 100 | 100 | 
| --- |--- |--- |--- |

After the instance fleet is launched, you can run [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations remain\.

**Example 2: Lowest\-price instance pool in launch request does not have available open Capacity Reservations**

In this case, Amazon EMR launches capacity in the lowest\-price instance pool with On\-Demand instances\. However, your open Capacity Reservations remain unused\.


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Requested Capacity | 100 | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
|  Available Open Capacity Reservations  | \- | \- | 100 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | 100 | \- | \- | 
| --- |--- |--- |--- |
| Open Capacity Reservation Used | \- | \- | \- | 
| --- |--- |--- |--- |
| Available Open Capacity Reservations | \- | \- | 100 | 
| --- |--- |--- |--- |

**Configure Instance Fleets to use open Capacity Reservations on best\-effort basis**

When you use the `RunJobFlow` action to create an instance fleet\-based cluster, set the On\-Demand allocation strategy to `lowest-price` and `CapacityReservationPreference` for Capacity Reservation Options to `open`, Or you can simply leave this field blank as Amazon EMR defaults the On\-demand instance's Capacity Reservation preference to `open`\.

```
"LaunchSpecifications": 
    {"OnDemandSpecification": {
        "AllocationStrategy": "lowest-price",
        "CapacityReservationOptions":
         {
            "CapacityReservationPreference": "open"
         }
        }
    }
```

You can also use the Amazon EMR CLI to create an instance fleet\-based cluster using open Capacity Reservations\.

```
aws emr create-cluster \
--name "open-ODCR-cluster" \
--release-label emr-5.30.0 \
--service-role EMR_DefaultRole \
--ec2-attributes SubnetId=subnet-22XXXX01,InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=c4.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=100,InstanceTypeConfigs=['{InstanceType=c5.xlarge},{InstanceType=m5.xlarge},{InstanceType=r5.xlarge}'],\
LaunchSpecifications={OnDemandSpecification='{AllocationStrategy=lowest-price,CapacityReservationOptions={CapacityReservationPreference=open}}'}
```

Where,
+ `open-ODCR-cluster` is replaced with the name of the cluster using open Capacity Reservations\.
+ `subnet-22XXXX01` is replaced with the subnet ID\.

## Use Open Capacity Reservations first<a name="on-demand-capacity-reservations-first"></a>

You can choose to override the lowest\-price allocation strategy and prioritize using available open Capacity Reservations first while provisioning an Amazon EMR cluster\. In this case, Amazon EMR evaluates all the instance pools with Capacity Reservations specified in the launch request and uses the one with the lowest\-price that has sufficient capacity to launch all the requested core nodes\. If none of the instance pools with Capacity Reservations have sufficient capacity for the requested core nodes, Amazon EMR falls back to the best\-effort case described in the previous topic\. That is, Amazon EMR re\-evaluates all the instance pools specified in the launch request and uses the one with the lowest\-price that has sufficient capacity to launch all the requested core nodes\. Available open Capacity Reservations that match the instance pool are applied automatically\. If available open Capacity Reservations do not match the instance pool, they remain unused\. 

Once the core nodes are provisioned, the availability zone is selected and fixed\. Amazon EMR provisions task nodes into instance pools with Capacity Reservations, starting with the lowest\-priced ones first, in the selected availability zone until all the task nodes are provisioned\. Amazon EMR uses the available open Capacity Reservations available across each instance pool in the selected availability zone first, and only if required, uses the lowest\-price strategy to provision any remaining task nodes\. 

The following are use cases of Amazon EMR capacity allocation logic for using open Capacity Reservations first\.

**Example 1: **Instance pool with available open Capacity Reservations in launch request has sufficient capacity for core nodes****

In this case, Amazon EMR launches capacity in the instance pool with available open Capacity Reservations regardless of instance pool price\. As a result, your open Capacity Reservations are used whenever possible, until all core nodes are provisioned\.


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Requested Capacity | 100 | 
| Usage Strategy | use\-capacity\-reservations\-first | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
| Available Open Capacity Reservactions | \- | \- | 150 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | \- | \- | 100 | 
| --- |--- |--- |--- |
| Open Capacity Reservation Used | \- | \- | 100 | 
| --- |--- |--- |--- |
| Available Open Capacity Reservations | \- | \- | 50 | 
| --- |--- |--- |--- |

**Example 2: **Instance pool with available open Capacity Reservations in launch request does not have sufficient capacity for core nodes****

In this case, Amazon EMR falls back to launching core nodes using lowest\-price strategy with a best\-effort to use Capacity Reservations\.


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Requested Capacity | 100 | 
| Usage Strategy | use\-capacity\-reservations\-first | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
| Available Open Capacity Reservactions | 10 | 50 | 50 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | 100 | \- | \- | 
| --- |--- |--- |--- |
| Open Capacity Reservation Used | 10 | \- | \- | 
| --- |--- |--- |--- |
| Available open Capacity Reservations | \- | 50 | 50 | 
| --- |--- |--- |--- |

After the instance fleet is launched, you can run [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations remain\.

**Configure Instance Fleets to use open Capacity Reservations first**

When you use the `RunJobFlow` action to create an instance fleet\-based cluster, set the On\-Demand allocation strategy to `lowest-price` and `UsageStrategy` for Capacity Reservation Options to `use-capacity-reservations-first`\.

```
"LaunchSpecifications": 
    {"OnDemandSpecification": {
        "AllocationStrategy": "lowest-price",
        "CapacityReservationOptions":
         {
            "UsageStrategy": "use-capacity-reservations-first"
         }
       }
    }
```

You can also use the EMR CLI to create an instance\-fleet based cluster using Capacity Reservations first\.

```
aws emr create-cluster \
--name "use-CR-first-cluster" \
--release-label emr-5.30.0 \
--service-role EMR_DefaultRole \
--ec2-attributes SubnetId=subnet-22XXXX01,InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=c4.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=100,InstanceTypeConfigs=['{InstanceType=c5.xlarge},{InstanceType=m5.xlarge},{InstanceType=r5.xlarge}'],\
LaunchSpecifications={OnDemandSpecification='{AllocationStrategy=lowest-price,CapacityReservationOptions={UsageStrategy=use-capacity-reservations-first}}'}
```

Where,
+ `use-CR-first-cluster` is replaced with the name of the cluster using open Capacity Reservations\.
+ `subnet-22XXXX01` is replaced with the subnet ID\.

## Use Targeted Capacity Reservations first<a name="on-demand-capacity-reservations-targeted"></a>

You can choose to override the lowest\-price allocation strategy and prioritize using available targeted Capacity Reservations first while provisioning an EMR cluster\. In this case, EMR evaluates all the instance pools with targeted Capacity Reservations specified in the launch request and picks the one with the lowest\-price that has sufficient capacity to launch all the requested core nodes\. If none of the instance pools with targeted Capacity Reservations have sufficient capacity for core nodes, Amazon EMR falls back to the best\-effort case described earlier\. That is, Amazon EMR re\-evaluates all the instance pools specified in launch request and picks the one with the lowest\-price that has sufficient capacity to launch all the requested core nodes\. Available open Capacity Reservations which match the instance pool get applied automatically, however, targeted Capacity Reservations remain unused\.

Once the core nodes are provisioned, the availability zone is selected and fixed\. EMR provisions task nodes into instance pools with targeted Capacity Reservations, starting with the lowest\-priced ones first, in the selected availability zone till all the task nodes are provisioned\. EMR tries to use the available targeted Capacity Reservations available across each instance pool in the selected availability zone first and only then, if required, uses the lowest\-price strategy to provision any remaining task nodes\.

The following are use cases of Amazon EMR capacity allocation logic for using targeted Capacity Reservations first\.

**Example 1: Instance pool with available targeted Capacity Reservations in launch request has sufficient capacity for core nodes**

In this case, Amazon EMR launches capacity in the instance pool with available targeted Capacity Reservations regardless of instance pool price\. As a result, your targeted Capacity Reservations are used whenever possible until all core nodes are provisioned\.


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Usage Strategy | use\-capacity\-reservations\-first | 
| Requested Capacity | 100 | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
| Available targeted Capacity Reservations | \- | \- | 150 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | \- | \- | 100 | 
| --- |--- |--- |--- |
| Targeted Capacity Reservation Used | \- | \- | 100 | 
| --- |--- |--- |--- |
| Available targeted Capacity Reservations | \- | \- | 50 | 
| --- |--- |--- |--- |

**Example 2: Instance pool with available targeted Capacity Reservations in launch request does not have sufficient capacity for core nodes**  


|  |  | 
| --- |--- |
| On\-Demand Strategy | lowest\-price | 
| Requested Capacity | 100 | 
| Usage Strategy | use\-capacity\-reservations\-first | 
| Instance Type | c5\.xlarge | m5\.xlarge | r5\.xlarge | 
| Available targeted Capacity Reservations | 10 | 50 | 50 | 
| On\-Demand Price | $ | $$ | $$$ | 
| Instances Provisioned | 100 | \- | \- | 
| --- |--- |--- |--- |
| Targeted Capacity Reservation Used | \- | \- | \- | 
| --- |--- |--- |--- |
| Available targeted Capacity Reservations | 10 | 50 | 50 | 
| --- |--- |--- |--- |

After the instance fleet is launched, you can run [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations remain\.

**Configure Instance Fleets to use targeted Capacity Reservations first**

When you use the `RunJobFlow` action to create an instance\-fleet based cluster, set the On\-Demand allocation strategy to `lowest-price`, `UsageStrategy` for Capacity Reservation Options to `use-capacity-reservations-first`, and `CapacityReservationResourceGroupArn` for Capacity Reservation Options to `<your resource group ARN>`\. For more information, see [Work with Capacity Reservations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-reservations-using.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
"LaunchSpecifications": 
    {"OnDemandSpecification": {
        "AllocationStrategy": "lowest-price",
        "CapacityReservationOptions":
         {
            "UsageStrategy": "use-capacity-reservations-first",
            "CapacityReservationResourceGroupArn": "arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup"
         }
       }
    }
```

Where `arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup` is replaced with your resource group ARN\.

You can also use the EMR CLI to create an instance fleet\-based cluster using targeted Capacity Reservations\.

```
aws emr create-cluster \
--name "targeted-CR-cluster" \
--release-label emr-5.30.0 \
--service-role EMR_DefaultRole \
--ec2-attributes SubnetId=subnet-22XXXX01,InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=c4.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=100,InstanceTypeConfigs=['{InstanceType=c5.xlarge},{InstanceType=m5.xlarge},{InstanceType=r5.xlarge}'],\
LaunchSpecifications={OnDemandSpecification='{AllocationStrategy=lowest-price,CapacityReservationOptions={UsageStrategy=use-capacity-reservations-first,CapacityReservationResourceGroupArn=arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup}}'}
```

Where,
+ `targeted-CR-cluster` is replaced with the name of your cluster using targeted Capacity Reservations\.
+ `subnet-22XXXX01` is replaced with the subnet ID\.
+ `arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup` is replaced with your resource group ARN\.

## Avoid using available open Capacity Reservations<a name="on-demand-capacity-reservations-avoiding"></a>

**Example**  
If you want to avoid unexpectedly using any of your open Capacity Reservations when launching an Amazon EMR cluster, set the On\-Demand allocation strategy to `lowest-price` and `CapacityReservationPreference` for Capacity Reservation Options to `none`\. Otherwise, Amazon EMR defaults the on\-demand instance's Capacity Reservation preference to `open` and tries using available open Capacity Reservations on a best\-effort basis\.  

```
"LaunchSpecifications": 
    {"OnDemandSpecification": {
        "AllocationStrategy": "lowest-price",
        "CapacityReservationOptions":
         {
            "CapacityReservationPreference": "none"
         }
       }
    }
```
You can also use the Amazon EMR CLI to create an instance fleet\-based cluster without using any open Capacity Reservations\.  

```
aws emr create-cluster \
--name "none-CR-cluster" \
--release-label emr-5.30.0 \
--service-role EMR_DefaultRole \
--ec2-attributes SubnetId=subnet-22XXXX01,InstanceProfile=EMR_EC2_DefaultRole \
--instance-fleets InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=c4.xlarge}'] \
InstanceFleetType=CORE,TargetOnDemandCapacity=100,InstanceTypeConfigs=['{InstanceType=c5.xlarge},{InstanceType=m5.xlarge},{InstanceType=r5.xlarge}'],\
LaunchSpecifications={OnDemandSpecification='{AllocationStrategy=lowest-price,CapacityReservationOptions={CapacityReservationPreference=none}}'}
```
Where,  
+ `none-CR-cluster` is replaced with the name of your cluster that is not using any open Capacity Reservations\.
+ `subnet-22XXXX01` is replaced with the subnet ID\.