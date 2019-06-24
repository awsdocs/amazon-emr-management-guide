# Instance Purchasing Options<a name="emr-instance-purchasing-options"></a>

When you set up a cluster, you choose a purchasing option for EC2 instances\. You can choose to use On\-Demand Instances, Spot Instances, or both\. Prices vary based on the instance type and region\. For current pricing, see [Amazon EMR Pricing](https://aws.amazon.com/emr/pricing)\.

Your choice to use instance groups or instance fleets in your cluster determines how you can change instance purchasing options while a cluster is running\. If you choose uniform instance groups, the instance type and purchasing option apply to all EC2 instances in each instance group, and you can only specify the purchasing option for an instance group when you create it\. If you choose instance fleets, you can change purchasing options after you create the instance fleet, and you can mix purchasing options to fulfill a target capacity that you specify\. For more information about these configurations, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](emr-instance-group-configuration.md)\.

**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## On\-Demand Instances<a name="emr-instances-on-demand"></a>

With On\-Demand Instances, you pay for compute capacity by the hour\. Optionally, you can have these On\-Demand Instances use Reserved Instance or Dedicated Instance purchasing options\. With Reserved Instances, you make a one\-time payment for an instance to reserve capacity\. Dedicated Instances are physically isolated at the host hardware level from instances that belong to other AWS accounts\. For more information about purchasing options, see [Instance Purchasing Options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Using Reserved Instances<a name="emr-instances-reserved"></a>

To use Reserved Instances in Amazon EMR, you use Amazon EC2 to purchase the Reserved Instance and specify the parameters of the reservation, including the scope of the reservation as applying to either a region or an Availability Zone\. For more information, see [Amazon EC2 Reserved Instances](https://aws.amazon.com//ec2/reserved-instances/) and [Buying Reserved Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ri-market-concepts-buying.html) in the *Amazon EC2 User Guide for Linux Instances*\. After you purchase a Reserved Instance, if all of the following conditions are true, Amazon EMR uses the Reserved Instance when a cluster launches:
+ An On\-Demand Instance is specified in the cluster configuration that matches the Reserved Instance specification
+ The cluster is launched within the scope of the instance reservation \(the Availability Zone or region\)
+ The Reserved Instance capacity is still available

For example, let's say you purchase one `m4.large` Reserved Instance with the instance reservation scoped to the US\-East region\. You then launch an EMR cluster in US\-East that uses two `m4.large` instances\. The first instance is billed at the Reserved Instance rate and the other is billed at the On\-Demand rate\. Reserved Instance capacity is used before any On\-Demand Instances are created\.

### Using Dedicated Instances<a name="emr-dedicated-instances"></a>

To use Dedicated Instances, you purchase Dedicated Instances using Amazon EC2 and then create a VPC with the **Dedicated** tenancy attribute\. Within Amazon EMR, you then specify that a cluster should launch in this VPC\. Any On\-Demand Instances in the cluster that match the Dedicated Instance specification use available Dedicated Instances when the cluster launches\.

**Note**  
Amazon EMR does not support setting the `dedicated` attribute on individual instances\.

## Spot Instances<a name="emr-spot-instances"></a>

Spot Instances in Amazon EMR provide an option for you to purchase Amazon EC2 instance capacity at a reduced cost as compared to On\-Demand purchasing\. The disadvantage of using Spot Instances is that instances may terminate unpredictably as prices fluctuate\. For more information about when using Spot Instances may be appropriate for your application, see [When Should You Use Spot Instances?](emr-plan-instances-guidelines.md#emr-plan-spot-instances)\.

When Amazon EC2 has unused capacity, it offers EC2 instances at a reduced cost, called the *Spot price*\. This price fluctuates based on availability and demand, and is established by region and Availability Zone\. When you choose Spot Instances, you specify the maximum Spot price that you're willing to pay for each EC2 instance type\. When the Spot price in the cluster's Availability Zone is below the maximum Spot price specified for that instance type, the instances launch\. While instances run, you're charged at the current Spot price *not your maximum Spot price*\. 

When you create a cluster with instance fleets, you have the option to use a *defined duration* \(also known as a Spot block\) which provides a greater degree of predictability\. Spot Instances terminate at the end of the duration, but are not interrupted before the duration expires\. This topic describes how Spot Instances work with Amazon EMR\.

For current pricing, see [Amazon EC2 Spot Instances Pricing](https://aws.amazon.com/ec2/spot/pricing/)\. For more information, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\. When you create and configure a cluster, you specify network options that ultimately determine the Availability Zone where your cluster launches\. For more information, see [Configure Networking](emr-plan-vpc-subnet.md)\. 

**Tip**  
You can see the real\-time Spot price in the console when you hover over the information tooltip next to the **Spot** purchasing option when you create a cluster using **Advanced Options**\. The prices for each Availability Zone in the selected region are displayed\. The lowest prices are in the green\-colored rows\. Because of fluctuating Spot prices between Availability Zones, selecting the Availability Zone with the lowest initial price might not result in the lowest price for the life of the cluster\. For optimal results, study the history of Availability Zone pricing before choosing\. For more information, see [Spot Instance Pricing History](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances-history.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Spot Instance options depend on whether you use uniform instance groups or instance fleets in your cluster configuration\.

****Spot Instances in Uniform Instance Groups****  
When you use Spot Instances in a uniform instance group, all instances in an instance group must be Spot Instances\. You specify a single subnet or Availability Zone for the cluster\. For each instance group, you specify a single Spot Instance type and a maximum Spot price\. Spot Instances of that type launch if the Spot price in the cluster's region and Availability Zone is below the maximum Spot price\. Instances terminate if the Spot price is above your maximum Spot price\. You set the maximum Spot price only when you configure an instance group\. It can’t be changed later\. For more information, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](emr-instance-group-configuration.md)\.

****Spot Instances in Instance Fleets****  
When you use the instance fleets configuration, additional options give you more control over how Spot Instances launch and terminate\. Fundamentally, instance fleets use a different method than uniform instance groups to launch instances\. The way it works is you establish a *target capacity* for Spot Instances \(and On\-Demand Instances\) and up to five instance types\. You can also specify a *weighted capacity* for each instance type or use the vCPU \(YARN vcores\) of the instance type as weighted capacity\. This weighted capacity counts toward your target capacity when an instance of that type is provisioned\. Amazon EMR provisions instances with both purchasing options until the target capacity for each target is fulfilled\. In addition, you can define a range of Availability Zones for Amazon EMR to choose from when launching instances\. You also provide additional Spot options for each fleet, including a provisioning timeout and, optionally, a defined duration\. For more information, see [Configure Instance Fleets](emr-instance-fleet.md)\.