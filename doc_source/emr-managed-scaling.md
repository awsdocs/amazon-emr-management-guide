# Using EMR managed scaling in Amazon EMR<a name="emr-managed-scaling"></a>

**Topics**
+ [Understanding node allocation strategy and scenarios](managed-scaling-allocation-strategy.md)
+ [Understanding managed scaling metrics](managed-scaling-metrics.md)
+ [Use the AWS Management Console to configure managed scaling](managed-scaling-console.md)
+ [Updated console options for cluster scaling](managed-scaling-console-updates.md)
+ [Using the AWS CLI to configure managed scaling](managed-scaling-cli.md)
+ [Using AWS SDK for Java to configure managed scaling](managed-scaling-sdk.md)

**Important**  
We strongly recommend that you use the latest Amazon EMR releases for managed scaling\. In earlier Amazon EMR versions, you could experience intermittent application failures or delay in scaling\. This issue was fixed in Amazon EMR 5\.30\.2, 5\.31\.1, 5\.32\.1, 5\.33\.1 and 6\.0\.1, 6\.1\.1, 6\.2\.1, 6\.3\.1\.

With Amazon EMR versions 5\.30\.0 and later \(except for Amazon EMR 6\.0\.0\), you can enable EMR managed scaling\. Managed scaling lets you automatically increase or decrease the number of instances or units in your cluster based on workload\. EMR continuously evaluates cluster metrics to make scaling decisions that optimize your clusters for cost and speed\. Managed scaling is available for clusters composed of either instance groups or instance fleets\.

You need to configure the following parameters for managed scaling\. The limit only applies to the core and task nodes\. The master node cannot be scaled after initial configuration\.
+ **Minimum** \(`MinimumCapacityUnits`\) – The lower boundary of allowed EC2 capacity in a cluster\. It is measured through virtual central processing unit \(vCPU\) cores or instances for instance groups\. It is measured through units for instance fleets\. 
+ **Maximum** \(`MaximumCapacityUnits`\) – The upper boundary of allowed EC2 capacity in a cluster\. It is measured through virtual central processing unit \(vCPU\) cores or instances for instance groups\. It is measured through units for instance fleets\. 
+ **On\-Demand limit** \(`MaximumOnDemandCapacityUnits`\) \(Optional\) – The upper boundary of allowed EC2 capacity for On\-Demand market type in a cluster\. If this parameter is not specified, it defaults to the value of `MaximumCapacityUnits`\. 

  This parameter is used to split capacity allocation between On\-Demand and Spot Instances\. For example, if you set the minimum parameter as 2 instances, the maximum parameter as 100 instances, the On\-Demand limit as 10 instances, then EMR managed scaling scales up to 10 On\-Demand Instances and allocates the remaining capacity to Spot Instances\. For more information, see [Node allocation scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\.
+ **Maximum core nodes **\(`MaximumCoreCapacityUnits`\) \(Optional\) – The upper boundary of allowed EC2 capacity for core node type in a cluster\. If this parameter is not specified, it defaults to the value of `MaximumCapacityUnits`\. 

  This parameter is used to split capacity allocation between core and task nodes\. For example, if you set the minimum parameter as 2 instances, the maximum as 100 instances, the maximum core node as 17 instances, then EMR managed scaling scales up to 17 core nodes and allocates the remaining 83 instances to task nodes\. For more information, see [Node allocation scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\. 

For more information about managed scaling parameters, see [https://docs.aws.amazon.com/emr/latest/APIReference/API_ComputeLimits.html](https://docs.aws.amazon.com/emr/latest/APIReference/API_ComputeLimits.html)\.

**Considerations and limitations**
+ If your YARN jobs are intermittently slow during scale down and YARN Resource Manager logs show that most of your nodes were deny listed during that time, you can adjust the decommissioning timeout threshold\. Reduce the `spark.blacklist.decommissioning.timeout` from one hour to one minute to make the node available for other pending containers to continue task processing\.

  You should also set `yarn.resourcemanager.nodemanager-graceful-decommission-timeout-secs` to a larger value to ensure Amazon EMR doesn’t force terminate the node while the longest “Spark Task” is still running on the node\. The current Default is 60 minutes, which means Yarn force terminates the container after 60 minutes once the node enters the decomissioning state\.

  Here is a sample Yarn Resource Manager Log line showing nodes are added to the decomissioning state\.

  ```
  2021-10-20 15:55:26,994 INFO org.apache.hadoop.yarn.server.resourcemanager.DefaultAMSProcessor (IPC Server handler 37 on default port 8030): blacklist are updated in Scheduler.blacklistAdditions: [ip-10-10-27-207.us-west-2.compute.internal, ip-10-10-29-216.us-west-2.compute.internal, ip-10-10-31-13.us-west-2.compute.internal, ... , ip-10-10-30-77.us-west-2.compute.internal], blacklistRemovals: []
  ```

  More [details on how Amazon EMR integrates with YARN deny listing during decommissioning of nodes](https://aws.amazon.com/blogs/big-data/spark-enhancements-for-elasticity-and-resiliency-on-amazon-emr/)\.

  [Cases when nodes in Amazon EMR can be deny listed](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-troubleshoot-error-resource-3.html)\.

  [Configuring Spark node decommissioning behavior](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-configure.html#spark-decommissioning)\.
+ Over\-utilization of EBS volumes can cause Managed Scaling issues\. We recommend that you maintain EBS volume below 90% utilization\. See [Specifying additional EBS storage volumes](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-storage.html)\.
+ EMR managed scaling is currently available in 22 AWS Regions: US East \(N\. Virginia and Ohio\), US West \(Oregon and N\. California\), South America \(São Paulo\), Europe \(Frankfurt, Ireland, London, Milan, Paris, and Stockholm\), Canada \(Central\), Asia Pacific \(Hong Kong, Mumbai, Seoul, Singapore, Sydney, and Tokyo\), Middle East \(Bahrain\), Africa \(Cape Town\), China \(Beijing\) operated by Sinnet, and China \(Ningxia\) operated by NWCD\.
+ Amazon CloudWatch metrics are critical for Amazon EMR managed scaling to operate\. We recommend that you closely monitor Amazon CloudWatch metrics to make sure data is not missing\. For more information about how you can configure CloudWatch alarms to detect missing metrics, see [Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\. 
+ Managed scaling operations on 5\.30\.0 and 5\.30\.1 clusters without Presto installed may cause application failures or cause a uniform instance group or instance fleet to stay in the `ARRESTED` state, particularly when a scale down operation is followed quickly by a scale up operation\.

  As a workaround, choose Presto as an application to install when you create a cluster, even if your job does not require Presto\.
+ EMR managed scaling only works with YARN applications, such as Spark, Hadoop, Hive, and Flink\. It currently does not support applications that are not based on YARN, such as Presto\.
+ When you set the maximum core node and the On\-Demand limit for EMR managed scaling, consider the differences between instance groups and instance fleets\. Each instance group consists of the same instance type and the same purchasing option for instances: On\-Demand or Spot\. For each instance fleet, you can specify up to five instance types, which can be provisioned as On\-Demand and Spot Instances\. For more information, see [Create a cluster with instance fleets or uniform instance groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-group-configuration.html), [Instance fleet options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-fleet.html#emr-instance-fleet-options), and [Node allocation scenarios](managed-scaling-allocation-strategy.md#node-allocation-scenarios)\.
+ On EMR 5\.30\.0 and later, if you remove the default "Allow All" outbound rule to 0\.0\.0\.0/ on the master security group, then you must at least add a rule to allow outbound TCP connectivity to Service Access Security Group on port \(9443\)\. Similarly, your Service Access Security Group should allow inbound TCP traffic on port 9443 from the master security group for managed scaling to work\. See [Amazon EMR\-managed security group for the master instance \(private subnets\)](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html#emr-sg-elasticmapreduce-master-private)\.
+ You can use AWS CloudFormation to configure EMR managed scaling\. For more information, see [AWS::EMR::Cluster](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticmapreduce-cluster.html) in the *AWS CloudFormation User Guide*\.

**Topics**
+ [Understanding node allocation strategy and scenarios](managed-scaling-allocation-strategy.md)
+ [Understanding managed scaling metrics](managed-scaling-metrics.md)
+ [Use the AWS Management Console to configure managed scaling](managed-scaling-console.md)
+ [Updated console options for cluster scaling](managed-scaling-console-updates.md)
+ [Using the AWS CLI to configure managed scaling](managed-scaling-cli.md)
+ [Using AWS SDK for Java to configure managed scaling](managed-scaling-sdk.md)