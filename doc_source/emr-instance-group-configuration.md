# Create a cluster with instance fleets or uniform instance groups<a name="emr-instance-group-configuration"></a>

When you create a cluster and specify the configuration of the master node, core nodes, and task nodes, you have two configuration options\. You can use *instance fleets* or *uniform instance groups*\. The configuration option you choose applies to all nodes, it applies for the lifetime of the cluster, and instance fleets and instance groups cannot coexist in a cluster\. The instance fleets configuration is available in Amazon EMR version 4\.8\.0 and later, excluding 5\.0\.x versions\. 

You can use the EMR console, the AWS CLI, or the EMR API to create clusters with either configuration\. When you use the `create-cluster` command from the AWS CLI, you use either the `--instance-fleets` parameters to create the cluster using instance fleets or, alternatively, you use the `--instance-groups` parameters to create it using uniform instance groups\.

The same is true using the EMR API\. You use either the `InstanceGroups` configuration to specify an array of `InstanceGroupConfig` objects, or you use the `InstanceFleets` configuration to specify an array of `InstanceFleetConfig` objects\.

In the EMR console, if you use the default **Quick Options** settings when you create a cluster, Amazon EMR applies the uniform instance groups configuration to the cluster and uses On\-Demand Instances\. To use Spot Instances with uniform instance groups, or to configure instance fleets and other customizations, choose **Advanced Options**\.

**Tip**  
To quickly and easily replicate a cluster you have already created, Amazon EMR gives you two options in the console\. You can clone the cluster or generate a `create cluster` CLI command\. First, choose **Cluster list** and then choose the cluster you want to replicate\. Choose **AWS CLI export** to have Amazon EMR generate the equivalent `create cluster` CLI command for the cluster, which you can then copy and paste\. Choose the **Clone** button to have Amazon EMR replicate your console setup\. Amazon EMR presents you with the last step of the **Advanced Options** to confirm the cluster's configuration\. You can either choose **Create cluster** to create the new cluster \(with the same name and a different cluster ID\), or you can choose **Previous** to go back and change settings\.

## Instance fleets<a name="emr-plan-instance-fleets"></a>

The instance fleets configuration offers the widest variety of provisioning options for Amazon EC2 instances\. Each node type has a single instance fleet, and using a task instance fleet is optional\. You can specify up to five EC2 instance types per fleet, or 30 EC2 instance types per fleet when you create a cluster using the AWS CLI or Amazon EMR API and an [allocation strategy](emr-instance-fleet.md#emr-instance-fleet-allocation-strategy) for On\-Demand and Spot Instances\. For the core and task instance fleets, you assign a *target capacity* for On\-Demand Instances, and another for Spot Instances\. Amazon EMR chooses any mix of the specified instance types to fulfill the target capacities, provisioning both On\-Demand and Spot Instances\.

For the master node type, Amazon EMR chooses a single instance type from your list of instances, and you specify whether it's provisioned as an On\-Demand or Spot Instance\. Instance fleets also provide additional options for Spot Instance and On\-Demand purchases\. Spot Instance options include a defined duration \(also known as a spot block\), a timeout that specifies an action to take if Spot capacity can't be provisioned, and a preferred allocation strategy \(capacity\-optimized\) for launching Spot Instance fleets\. On\-Demand Instance fleets can also be launched using the allocation strategy \(lowest\-price\) option\. If you use a service role that is not the EMR default service role, or use an EMR managed policy in your service role, you need to add additional permissions to the custom cluster service role to enable the allocation strategy option\. For more information, see [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)\.

For more information about configuring instance fleets, see [Configure instance fleets](emr-instance-fleet.md)\.

## Uniform instance groups<a name="emr-plan-instance-groups"></a>

Uniform instance groups offer a simpler setup than instance fleets\. Each Amazon EMR cluster can include up to 50 instance groups: one master instance group that contains one EC2 instance, a core instance group that contains one or more EC2 instances, and up to 48 optional task instance groups\. Each core and task instance group can contain any number of EC2 instances\. You can scale each instance group by adding and removing EC2 instances manually, or you can set up automatic scaling\. For information about adding and removing instances, see [Scaling cluster resources](emr-scale-on-demand.md)\.

For more information about configuring uniform instance groups, see [Configure uniform instance groups](emr-uniform-instance-group.md)\. 

**Topics**
+ [Instance fleets](#emr-plan-instance-fleets)
+ [Uniform instance groups](#emr-plan-instance-groups)
+ [Configure instance fleets](emr-instance-fleet.md)
+ [Use capacity reservations with instance fleets](on-demand-capacity-reservations.md)
+ [Configure uniform instance groups](emr-uniform-instance-group.md)
+ [Cluster configuration guidelines and best practices](emr-plan-instances-guidelines.md)