# Cluster Configuration Guidelines and Best Practices<a name="emr-plan-instances-guidelines"></a>

Use the guidance in this section to help you determine the instance types, purchasing options, and amount of storage to provision for each node type in an EMR cluster\.

## What Instance Type Should You Use?<a name="emr-instance-group-size"></a>

When you plan the instance types and number of instances that your cluster uses, we recommend that you run a test cluster with a representative sample set of data and monitor the utilization of the nodes in the cluster\. For more information, see [View and Monitor a Cluster](emr-manage-view.md)\. Before testing, review the guidelines in this section to find a starting point\.

By default, the total number of EC2 instances you can run on a single AWS account is 20\. This means that the total number of nodes you can have in a cluster is 20\. For more information about how to request a quota increase, see [AWS service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

The compute power and memory that each node requires depends on several factors:
+ The size of the datasets that you process and how quickly you need results\.
+ The suite of big data applications that you use\.
+ The number of nodes in the instance group that can share the task execution and storage burden\.

Most Amazon EMR clusters can run on [general purpose instance types](http://aws.amazon.com/ec2/instance-types/#General_Purpose) such as m5\.xlarge\. Computation\-intensive clusters that demand fast output may benefit from running on [compute optimized](http://aws.amazon.com/ec2/instance-types/#Compute_Optimized) or [accelerated computing](http://aws.amazon.com/ec2/instance-types/#Accelerated_Computing) instances\. Database and memory\-caching applications may benefit from running on [memory optimized](http://aws.amazon.com/ec2/instance-types/#Memory_Optimized) instances\. Network\-intensive and CPU\-intensive applications like parsing, natural language processing, and machine learning may benefit from running on instances that offer enhanced networking\. Instances that support enhanced networking often have an "n" in their identifier\. For more information, see the [Instance Type Explorer](http://aws.amazon.com/ec2/instance-explorer/)\.

In addition, consider the following guidelines based on node type\.

### Master Node Considerations<a name="emr-master-node-instance-type"></a>

In general, the larger a cluster and the larger the dataset, the more memory the master node needs\. The default m5\.xlarge instance can be a good starting point, but consider using instance types with more memory, such as \*\.2xlarge or \*\.4xlarge, as a cluster grows\.

For Spark workloads, consider increasing the memory capacity of the master node to an instance type that has 32 GiB of memory or more to avoid memory fragmentation\. 32 GiB should be adequate for most applications\. Consider using an instance with even more memory capacity if you run multiple sessions or use client mode with drivers running on the master node\. This includes notebook applications where Zeppelin or Jupyter may run multiple notebook sessions\.

When choosing the features of the master node, consider metadata processing requirements\. Large clusters using local disk storage and HDFS might need additional memory to accommodate the HDFS NameNode daemon\. HBase clusters with large datasets might need additional memory to accommodate the HBase HMaster\. Clusters using Hive and Spark SQL might need additional memory for a large metastore with many partitions\.

For Presto workloads, consider the expected size of query results\. Larger query results place greater demands on the master node, which will likely benefit from more memory capacity\.

### Core and Task Node Considerations<a name="emr-core-node-instance-type"></a>

The amount of data you can process depends on the storage capacity of your core nodes and the size of your data as input, during processing, and as output\. The input, intermediate, and output datasets are stored on the cluster during processing\.

The computational needs of the core and task nodes depend on the type of processing that your application performs\. Adding task nodes can help alleviate the processing burden on the core nodes\. Many jobs can be run on m5\.xlarge\. If your application has external dependencies that introduce delays, such as web crawling to collect data, you may be able to run the cluster on less capable instances to reduce costs\. The instances can take more time to run jobs while external dependencies finish\.

For improved performance, consider increasing the memory capacity, the processing power, or both for core and task nodes\. If different phases of your cluster have varying capacity requirements, you can start with a small number of core or task nodes and then increase or decrease the number on the fly\.

For Spark workloads, consider that Spark reserves 8 GiB of memory for itself, which is a substantial baseline memory overhead\. For Spark workloads, consider instances with additional memory to accommodate that overhead and Spark executors\.

## When Should You Use Spot Instances?<a name="emr-plan-spot-instances"></a>

When you launch a cluster in Amazon EMR, you can choose to launch master, core, or task instances on Spot Instances\. Because each type of instance group plays a different role in the cluster, there are implications of launching each node type on Spot Instances\. You can't change an instance purchasing option while a cluster is running\. To change from On\-Demand to Spot Instances or vice versa, for the master and core nodes, you must terminate the cluster and launch a new one\. For task nodes, you can launch a new task instance group or instance fleet, and remove the old one\.

**Topics**
+ [Amazon EMR Settings To Prevent Job Failure Because of Task Node Spot Instance Termination](#emr-plan-spot-YARN)
+ [Master Node on a Spot Instance](#emr-dev-master-instance-group-spot)
+ [Core Nodes on Spot Instances](#emr-dev-core-instance-group-spot)
+ [Task Nodes on Spot Instances](#emr-dev-task-instance-group-spot)
+ [Instance Configurations for Application Scenarios](#emr-plan-spot-scenarios)

### Amazon EMR Settings To Prevent Job Failure Because of Task Node Spot Instance Termination<a name="emr-plan-spot-YARN"></a>

Because Spot Instances are often used to run task nodes, Amazon EMR has default functionality for scheduling YARN jobs so that running jobs do not fail when task nodes running on Spot Instances are terminated\. Amazon EMR does this by allowing application master processes to run only on core nodes\. The application master process controls running jobs and needs to stay alive for the life of the job\.

Amazon EMR release version 5\.19\.0 and later uses the built\-in [YARN node labels](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/NodeLabel.html) feature to achieve this\. \(Earlier versions used a code patch\)\. Properties in the `yarn-site` and `capacity-scheduler` configuration classifications are configured by default so that the YARN capacity\-scheduler and fair\-scheduler take advantage of node labels\. Amazon EMR automatically labels core nodes with the `CORE` label, and sets properties so that application masters are scheduled only on nodes with the CORE label\. Manually modifying related properties in the yarn\-site and capacity\-scheduler configuration classifications, or directly in associated XML files, could break this feature or modify this functionality\.

Amazon EMR configures the following properties and values by default\. Use caution when configuring these properties\.
+ **yarn\-site \(yarn\-site\.xml\) On All Nodes**
  + `yarn.node-labels.enabled: true`
  + `yarn.node-labels.am.default-node-label-expression: 'CORE'`
  + `yarn.node-labels.fs-store.root-dir: '/apps/yarn/nodelabels'`
  + `yarn.node-labels.configuration-type: 'distributed'`
+ **yarn\-site \(yarn\-site\.xml\) On Master And Core Nodes**
  + `yarn.nodemanager.node-labels.provider: 'config'`
  + `yarn.nodemanager.node-labels.provider.configured-node-partition: 'CORE'`
+ **capacity\-scheduler \(capacity\-scheduler\.xml\) On All Nodes**
  + `yarn.scheduler.capacity.root.accessible-node-labels: '*'`
  + `yarn.scheduler.capacity.root.accessible-node-labels.CORE.capacity: 100`
  + `yarn.scheduler.capacity.root.default.accessible-node-labels: '*'`
  + `yarn.scheduler.capacity.root.default.accessible-node-labels.CORE.capacity: 100`

**Note**  
Beginning with Amazon EMR 6\.x release series, the YARN node labels feature is disabled by default\. The application master processes can run on both core and task nodes by default\. You can enable the YARN node labels feature by configuring following properties:   
`yarn.node-labels.enabled: true`
`yarn.node-labels.am.default-node-label-expression: 'CORE'`

### Master Node on a Spot Instance<a name="emr-dev-master-instance-group-spot"></a>

The master node controls and directs the cluster\. When it terminates, the cluster ends, so you should only launch the master node as a Spot Instance if you are running a cluster where sudden termination is acceptable\. This might be the case if you are testing a new application, have a cluster that periodically persists data to an external store such as Amazon S3, or are running a cluster where cost is more important than ensuring the cluster’s completion\. 

When you launch the master instance group as a Spot Instance, the cluster does not start until that Spot Instance request is fulfilled\. This is something to consider when selecting your maximum Spot price\.

You can only add a Spot Instance master node when you launch the cluster\. Master nodes cannot be added or removed from a running cluster\. 

Typically, you would only run the master node as a Spot Instance if you are running the entire cluster \(all instance groups\) as Spot Instances\. 

### Core Nodes on Spot Instances<a name="emr-dev-core-instance-group-spot"></a>

Core nodes process data and store information using HDFS\. Terminating a core instance risks data loss\. For this reason, you should only run core nodes on Spot Instances when partial HDFS data loss is tolerable\.

When you launch the core instance group as Spot Instances, Amazon EMR waits until it can provision all of the requested core instances before launching the instance group\. In other words, if you request six Amazon EC2 instances, and only five are available at or below your maximum Spot price, the instance group won't launch\. Amazon EMR continues to wait until all six Amazon EC2 instances are available or until you terminate the cluster\. You can change the number of Spot Instances in a core instance group to add capacity to a running cluster\. For more information about working with instance groups, and how Spot Instances work with instance fleets, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](emr-instance-group-configuration.md)\.

### Task Nodes on Spot Instances<a name="emr-dev-task-instance-group-spot"></a>

The task nodes process data but do not hold persistent data in HDFS\. If they terminate because the Spot price has risen above your maximum Spot price, no data is lost and the effect on your cluster is minimal\.

When you launch one or more task instance groups as Spot Instances, Amazon EMR provisions as many task nodes as it can, using your maximum Spot price\. This means that if you request a task instance group with six nodes, and only five Spot Instances are available at or below your maximum Spot price, Amazon EMR launches the instance group with five nodes, adding the sixth later if possible\. 

Launching task instance groups as Spot Instances is a strategic way to expand the capacity of your cluster while minimizing costs\. If you launch your master and core instance groups as On\-Demand Instances, their capacity is guaranteed for the run of the cluster\. You can add task instances to your task instance groups as needed, to handle peak traffic or speed up data processing\. 

You can add or remove task nodes using the console, AWS CLI, or API\. You can also add additional task groups, but you cannot remove a task group after it is created\. 

### Instance Configurations for Application Scenarios<a name="emr-plan-spot-scenarios"></a>

The following table is a quick reference to node type purchasing options and configurations that are usually appropriate for various application scenarios\. Choose the link to view more information about each scenario type\.


| Application Scenario | Master Node Purchasing Option | Core Nodes Purchasing Option | Task Nodes Purchasing Option | 
| --- | --- | --- | --- | 
| [Long\-Running Clusters and Data Warehouses](#emr-dev-when-use-spot-data-warehouses) | On\-Demand | On\-Demand or instance\-fleet mix | Spot or instance\-fleet mix | 
| [Cost\-Driven Workloads](#emr-dev-when-use-spot-cost-driven) | Spot | Spot | Spot | 
| [Data\-Critical Workloads](#emr-dev-when-use-spot-data-critical) | On\-Demand | On\-Demand | Spot or instance\-fleet mix | 
| [Application Testing](#emr-dev-when-use-spot-application-testing) | Spot | Spot | Spot | 

 There are several scenarios in which Spot Instances are useful for running an Amazon EMR cluster\. 

#### Long\-Running Clusters and Data Warehouses<a name="emr-dev-when-use-spot-data-warehouses"></a>

If you are running a persistent Amazon EMR cluster that has a predictable variation in computational capacity, such as a data warehouse, you can handle peak demand at lower cost with Spot Instances\. You can launch your master and core instance groups as On\-Demand Instances to handle the normal capacity and launch task instance groups as Spot Instances to handle your peak load requirements\.

#### Cost\-Driven Workloads<a name="emr-dev-when-use-spot-cost-driven"></a>

If you are running transient clusters for which lower cost is more important than the time to completion, and losing partial work is acceptable, you can run the entire cluster \(master, core, and task instance groups\) as Spot Instances to benefit from the largest cost savings\.

#### Data\-Critical Workloads<a name="emr-dev-when-use-spot-data-critical"></a>

If you are running a cluster for which lower cost is more important than time to completion, but losing partial work is not acceptable, launch the master and core instance groups as on\-demand and supplement with one or more task instance groups of Spot Instances\. Running the master and core instance groups as on\-demand ensures that your data is persisted in HDFS and that the cluster is protected from termination due to Spot market fluctuations, while providing cost savings that accrue from running the task instance groups as Spot Instances\.

#### Application Testing<a name="emr-dev-when-use-spot-application-testing"></a>

When you are testing a new application in order to prepare it for launch in a production environment, you can run the entire cluster \(master, core, and task instance groups\) as Spot Instances to reduce your testing costs\.

## Calculating the Required HDFS Capacity of a Cluster<a name="emr-plan-instances-hdfs"></a>

 The amount of HDFS storage available to your cluster depends on these factors:
+ The number of EC2 instances used for core nodes\.
+ The capacity of the EC2 instance store for the instance type used\. For more information on instance store volumes, see [Amazon EC2 Instance Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ The number and size of EBS volumes attached to core nodes\.
+ A replication factor, which accounts for how each data block is stored in HDFS for RAID\-like redundancy\. By default, the replication factor is three for a cluster of 10 or more core nodes, two for a cluster of 4\-9 core nodes, and one for a cluster of three or fewer nodes\.

To calculate the HDFS capacity of a cluster, for each core node, add the instance store volume capacity to the EBS storage capacity \(if used\)\. Multiply the result by the number of core nodes, and then divide the total by the replication factor based on the number of core nodes\. For example, a cluster with 10 core nodes of type i2\.xlarge, which have 800 GB of instance storage without any attached EBS volumes, has a total of approximately 2,666 GB available for HDFS \(10 nodes x 800 GB ÷ 3 replication factor\)\.

 If the calculated HDFS capacity value is smaller than your data, you can increase the amount of HDFS storage in the following ways: 
+ Creating a cluster with additional EBS volumes or adding instance groups with attached EBS volumes to an existing cluster
+ Adding more core nodes
+ Choosing an EC2 instance type with greater storage capacity
+ Using data compression
+ Changing the Hadoop configuration settings to reduce the replication factor

Reducing the replication factor should be used with caution as it reduces the redundancy of HDFS data and the ability of the cluster to recover from lost or corrupted HDFS blocks\. 