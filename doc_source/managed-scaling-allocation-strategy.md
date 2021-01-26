# Understanding Node Allocation Strategy and Scenarios<a name="managed-scaling-allocation-strategy"></a>

This section gives an overview of node allocation strategy and common scaling scenarios that you can use with EMR managed scaling\. 

## Node Allocation Strategy<a name="node-allocation-strategy"></a>

EMR managed scaling allocates core and task nodes based on the following scale\-up and scale\-down strategies: 

**Scale\-up strategy **
+ EMR managed scaling first adds capacity to core nodes and then to task nodes until the maximum allowed capacity is reached or until the desired scale\-up target capacity is achieved\. 
+ If the `MaximumCoreCapacityUnits` parameter is set, then Amazon EMR scales core nodes until the core units reach the maximum allowed limit\. All the remaining capacity is added to task nodes\. 
+ If the `MaximumOnDemandCapacityUnits` parameter is set, then Amazon EMR scales the cluster by using the On\-Demand Instances until the On\-Demand units reach the maximum allowed limit\. All the remaining capacity is added using Spot Instances\. 
+ If both the `MaximumCoreCapacityUnits` and `MaximumOnDemandCapacityUnits` parameters are set, Amazon EMR considers both limits during scaling\. 

  For example, if the `MaximumCoreCapacityUnits` is less than `MaximumOnDemandCapacityUnits`, EMR first scales core nodes until the core capacity limit is reached\. For the remaining capacity, EMR first uses On\-Demand Instances to scale task nodes until the On\-Demand limit is reached, and then uses Spot Instances for task nodes\. 

**Scale\-down strategy**
+ EMR managed scaling first removes task nodes and then removes core nodes until the desired scale\-down target capacity is achieved\. The cluster never scales below the minimum constraints in the managed scaling policy\.
+ Within each node type \(either core nodes or task nodes\), EMR managed scaling removes Spot Instances first and then removes On\-Demand Instances\.

If the cluster does not have any load, then Amazon EMR cancels the addition of new instances from a previous evaluation and performs scale\-down operations\. If the cluster has a heavy load, Amazon EMR cancels the removal of instances and performs scale\-up operations\.

## Node Allocation Considerations<a name="node-allocation-considerations"></a>

We recommend that you use the On\-Demand purchasing option for core nodes to avoid HDFS data loss in case of Spot reclamation\. You can use the Spot purchasing option for task nodes to reduce costs and get faster job execution when more Spot Instances are added to task nodes\.

## Node Allocation Scenarios<a name="node-allocation-scenarios"></a>

You can create various scaling scenarios based on your needs by setting up the Maximum, Minimum, On\-Demand limit, and Maximum core node parameters in different combinations\. 

****Scenario 1: Scale Core Nodes Only****

To scale core nodes only, the managed scaling parameters must meet the following requirements: 
+ The On\-Demand limit is equal to the maximum boundary\.
+ The maximum core node is equal to the maximum boundary\. 

When the On\-Demand limit and the maximum core node parameters are not specified, both parameters default to the maximum boundary\. 

The following examples demonstrate the scenario of scaling cores nodes only\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/managed-scaling-allocation-strategy.html)

**Scenario 2: Scale task nodes only **

To scale task nodes only, the managed scaling parameters must meet the following requirement: 
+ The maximum core node must be equal to the minimum boundary\.

The following examples demonstrate the scenario of scaling task nodes only\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/managed-scaling-allocation-strategy.html)

**Scenario 3: Only On\-Demand Instances in the cluster **

To have On\-Demand Instances only, your cluster and the managed scaling parameters must meet the following requirement: 
+ The On\-Demand limit is equal to the maximum boundary\. 

  When the On\-Demand limit is not specified, the parameter value defaults to the maximum boundary\. The default value indicates that Amazon EMR scales On\-Demand Instances only\. 

If the maximum core node is less than the maximum boundary, the maximum core node parameter can be used to split capacity allocation between core and task nodes\. 

To enable this scenario in a cluster composed of instance groups, all node groups in the cluster must use the On\-Demand market type during initial configuration\. 

The following examples demonstrate the scenario of having On\-Demand Instances in the entire cluster\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/managed-scaling-allocation-strategy.html)

**Scenario 4: Only Spot Instances in the cluster**

To have Spot Instances only, the managed scaling parameters must meet the following requirement: 
+ On\-Demand limit is set to 0\.

If the maximum core node is less than the maximum boundary, the maximum core node parameter can be used to split capacity allocation between core and task nodes\.

To enable this scenario in a cluster composed of instance groups, the core instance group must use the Spot purchasing option during initial configuration\. If there is no Spot Instance in the task instance group, EMR managed scaling creates a task group using Spot Instances when needed\. 

The following examples demonstrate the scenario of having Spot Instances in the entire cluster\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/managed-scaling-allocation-strategy.html)

**Scenario 5: Scale On\-Demand Instances on core nodes and Spot Instances on task nodes **

To scale On\-Demand Instances on core nodes and Spot Instances on task nodes, the managed scaling parameters must meet the following requirements: 
+ The On\-Demand limit must be equal to the maximum core node\.
+ Both the On\-Demand limit and the maximum core node must be less than the maximum boundary\.

To enable this scenario in a cluster composed of instance groups, the core node group must use the On\-Demand purchasing option\.

The following examples demonstrate the scenario of scaling On\-Demand Instances on core nodes and Spot Instances on task nodes\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/managed-scaling-allocation-strategy.html)