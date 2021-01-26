# Using EMR Managed Scaling in Amazon EMR<a name="emr-managed-scaling"></a>

With Amazon EMR versions 5\.30\.0 and later \(except for Amazon EMR 6\.0\.0\), you can enable EMR managed scaling to automatically increase or decrease the number of instances or units in your cluster based on workload\. EMR continuously evaluates cluster metrics to make scaling decisions that optimize your clusters for cost and speed\. This feature is available for clusters composed of either instance groups or instance fleets\.

You need to configure the following parameters for managed scaling\. The limit only applies to the core and task nodes\. The master node cannot be scaled after initial configuration\.
+ **Minimum** \(`MinimumCapacityUnits`\) – The lower boundary of allowed EC2 capacity in a cluster\. It is measured through virtual central processing unit \(vCPU\) cores or instances for instance groups\. It is measured through units for instance fleets\. 
+ **Maximum** \(`MaximumCapacityUnits`\) – The upper boundary of allowed EC2 capacity in a cluster\. It is measured through virtual central processing unit \(vCPU\) cores or instances for instance groups\. It is measured through units for instance fleets\. 
+ **On\-Demand limit** \(`MaximumOnDemandCapacityUnits`\) \(Optional\) – The upper boundary of allowed EC2 capacity for On\-Demand market type in a cluster\. If this parameter is not specified, it defaults to the value of `MaximumCapacityUnits`\. 

  This parameter is used to split capacity allocation between On\-Demand and Spot Instances\. For example, if you set the minimum parameter as 2 instances, the maximum parameter as 100 instances, the On\-Demand limit as 10 instances, then EMR managed scaling scales up to 10 On\-Demand Instances and allocates the remaining capacity to Spot Instances\. For more information, see [Node Allocation Scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\.
+ **Maximum core nodes **\(`MaximumCoreCapacityUnits`\) \(Optional\) – The upper boundary of allowed EC2 capacity for core node type in a cluster\. If this parameter is not specified, it defaults to the value of `MaximumCapacityUnits`\. 

  This parameter is used to split capacity allocation between core and task nodes\. For example, if you set the minimum parameter as 2 instances, the maximum as 100 instances, the maximum core node as 17 instances, then EMR managed scaling scales up to 17 core nodes and allocates the remaining 83 instances to task nodes\. For more information, see [Node Allocation Scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\. 

For more information about managed scaling parameters, see [https://docs.aws.amazon.com/emr/latest/APIReference/API_ComputeLimits.html](https://docs.aws.amazon.com/emr/latest/APIReference/API_ComputeLimits.html)\.

**Considerations and limitations**
+ EMR managed scaling is currently available in 22 AWS Regions: US East \(N\. Virginia and Ohio\), US West \(Oregon and N\. California\), South America \(São Paulo\), Europe \(Frankfurt, Ireland, London, Milan, Paris, and Stockholm\), Canada \(Central\), Asia Pacific \(Hong Kong, Mumbai, Seoul, Singapore, Sydney, and Tokyo\), Middle East \(Bahrain\), Africa \(Cape Town\), China \(Beijing\) operated by Sinnet, and China \(Ningxia\) operated by NWCD\.
+ Managed scaling operations on 5\.30\.0 and 5\.30\.1 clusters without Presto installed may cause application failures or cause a uniform instance group or instance fleet to stay in the `ARRESTED` state, particularly when a scale down operation is followed quickly by a scale up operation\.

  As a workaround, choose Presto as an application to install when you create a cluster, even if your job does not require Presto\.
+ EMR managed scaling only works with YARN applications, such as Spark, Hadoop, Hive, and Flink\. It currently does not support applications that are not based on YARN, such as Presto\.
+ When you set the maximum core node and the On\-Demand limit for EMR managed scaling, consider the differences between instance groups and instance fleets\. Each instance group consists of the same instance type and the same purchasing option for instances: On\-Demand or Spot\. For each instance fleet, you can specify up to five instance types, which can be provisioned as On\-Demand and Spot Instances\. For more information, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-group-configuration.html), [Instance Fleet Options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-fleet.html#emr-instance-fleet-options), and [Node Allocation Scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\.
+ On EMR 5\.30\.0 and later, if you remove the default "Allow All" outbound rule to 0\.0\.0\.0/ on the master security group, then you must at least add a rule to allow outbound TCP connectivity to Service Access Security Group on port \(9443\)\. Similarly, your Service Access Security Group should allow inbound TCP traffic on port 9443 from the master security group for managed scaling to work\. See [Amazon EMR\-Managed Security Group for the Master Instance \(Private Subnets\)](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html#emr-sg-elasticmapreduce-master-private)\.
+ You can use AWS CloudFormation to configure EMR managed scaling\. For more information, see [AWS::EMR::Cluster](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticmapreduce-cluster.html) in the *AWS CloudFormation User Guide*\.

**Topics**
+ [Understanding Node Allocation Strategy and Scenarios](managed-scaling-allocation-strategy.md)
+ [Understanding Managed Scaling Metrics](managed-scaling-metrics.md)
+ [Using the AWS Management Console to Configure Managed Scaling](managed-scaling-console.md)
+ [Updated Console Options for Cluster Scaling](managed-scaling-console-updates.md)
+ [Using the AWS CLI to Configure Managed Scaling](managed-scaling-cli.md)
+ [Using AWS SDK for Java to Configure Managed Scaling](managed-scaling-sdk.md)