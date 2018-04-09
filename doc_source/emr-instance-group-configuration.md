# Create a Cluster with Instance Fleets or Uniform Instance Groups<a name="emr-instance-group-configuration"></a>

When you create a cluster and specify the configuration of the master node, core nodes, and task nodes, you have two configuration options\. You can use *instance fleets* or *uniform instance groups*\. The configuration option you choose applies to all nodes, it applies for the lifetime of the cluster, and instance fleets and instance groups cannot coexist in a cluster\. The instance fleets configuration is available in Amazon EMR version 4\.8\.0 and later, excluding 5\.0\.x versions\. 

You can use the EMR console, the AWS CLI, or the EMR API to create clusters with either configuration\. When you use the `create-cluster` command from the AWS CLI, you use either the `--instance-fleets` parameters to create the cluster using instance fleets or, alternatively, you use the `--instance-groups` parameters to create it using uniform instance groups\.

The same is true using the EMR API\. You use either the `InstanceGroups` configuration to specify an array of `InstanceGroupConfig` objects, or you use the `InstanceFleets` configuration to specify an array of `InstanceFleetConfig` objects\.

In the EMR console, if you use the default **Quick Options** settings when you create a cluster, Amazon EMR applies the uniform instance groups configuration to the cluster and uses On\-Demand Instances\. To use Spot Instances with uniform instance groups, or to configure instance fleets and other customizations, choose **Advanced Options**\.

**Tip**  
To quickly and easily replicate a cluster you have already created, Amazon EMR gives you two options in the console\. You can clone the cluster or generate a `create cluster` CLI command\. First, choose **Cluster list** and then choose the cluster you want to replicate\. Choose **AWS CLI export** to have Amazon EMR generate the equivalent `create cluster` CLI command for the cluster, which you can then copy and paste\. Choose the **Clone** button to have Amazon EMR replicate your console setup\. Amazon EMR presents you with the last step of the **Advanced Options** to confirm the cluster's configuration\. You can either choose **Create cluster** to create the new cluster \(with the same name and a different cluster ID\), or you can choose **Previous** to go back and change settings\.

**Topics**
+ [Configure Instance Fleets](emr-instance-fleet.md)
+ [Configure Uniform Instance Groups](emr-uniform-instance-group.md)
+ [Cluster Configuration Guidelines](emr-plan-instances-guidelines.md)