# Cluster Configuration Guidelines<a name="emr-plan-instances-guidelines"></a>

Use the guidance in this section to help you determine the instance types, purchasing options, and amount of storage to provision for each node type in an EMR cluster\.

## What Instance Type Should You Use?<a name="emr-instance-group-size"></a>

There are several ways to add EC2 instances to a cluster, which depend on whether you use the instance groups configuration or the instance fleets configuration for the cluster\.
+ **Instance Groups**
  + Manually add instances of the same type to existing core and task instance groups\.
  + Manually add a task instance group, which can use a different instance type\.
  + Set up automatic scaling in Amazon EMR for an instance group, adding and removing instances automatically based on the value of an Amazon CloudWatch metric that you specify\. For more information, see [Scaling Cluster Resources](emr-scale-on-demand.md)\.
+ **Instance Fleets**
  + Add a single task instance fleet\.
  + Change the target capacity for On\-Demand and Spot Instances for existing core and task instance fleets\. For more information, see [Configure Instance Fleets](emr-instance-fleet.md)\.

One way to plan the instances of your cluster is to run a test cluster with a representative sample set of data and monitor the utilization of the nodes in the cluster\. For more information, see [View and Monitor a Cluster](emr-manage-view.md)\. Another way is to calculate the capacity of the instances you are considering and compare that value against the size of your data\.

In general, the master node type, which assigns tasks, doesn't require an EC2 instance with much processing power; EC2 instances for the core node type, which process tasks and store data in HDFS, need both processing power and storage capacity; EC2 instances for the task node type, which don't store data, need only processing power\. For guidelines about available EC2 instances and their configuration, see [Plan and Configure EC2 Instances](emr-plan-ec2-instances.md)\.

 The following guidelines apply to most Amazon EMR clusters\. 
+ The master node does not have large computational requirements\. For most clusters of 50 or fewer nodes, consider using an m4\.large instance\. For clusters of more than 50 nodes, consider using an m4\.xlarge\.
+ The computational needs of the core and task nodes depend on the type of processing your application performs\. Many jobs can be run on m4\.large instance types, which offer balanced performance in terms of CPU, disk space, and input/output\. If your application has external dependencies that introduce delays \(such as web crawling to collect data\), you may be able to run the cluster on t2\.medium instances to reduce costs while the instances are waiting for dependencies to finish\. For improved performance, consider running the cluster using m4\.xlarge instances for the core and task nodes\. If different phases of your cluster have different capacity needs, you can start with a small number of core nodes and increase or decrease the number of task nodes to meet your job flow's varying capacity requirements\. 
+ Most Amazon EMR clusters can run on standard EC2 instance types such as m4\.large and m4\.xlarge\. Computation\-intensive clusters may benefit from running on High CPU instances, which have proportionally more CPU than RAM\. Database and memory\-caching applications may benefit from running on High Memory instances\. Network\-intensive and CPU\-intensive applications like parsing, NLP, and machine learning may benefit from running on Cluster Compute instances, which provide proportionally high CPU resources and increased network performance\.
+ The amount of data you can process depends on the capacity of your core nodes and the size of your data as input, during processing, and as output\. The input, intermediate, and output datasets all reside on the cluster during processing\. 
+ By default, the total number of EC2 instances you can run on a single AWS account is 20\. This means that the total number of nodes you can have in a cluster is 20\. For more information about how to request a limit increase for your account, see [AWS Limits](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\. 

## When Should You Use Spot Instances?<a name="emr-plan-spot-instances"></a>

 There are several scenarios in which Spot Instances are useful for running an Amazon EMR cluster\. 
<a name="emr-dev-when-use-spot-data-warehouses"></a>
**Long\-Running Clusters and Data Warehouses**  
If you are running a persistent Amazon EMR cluster that has a predictable variation in computational capacity, such as a data warehouse, you can handle peak demand at lower cost with Spot Instances\. You can launch your master and core instance groups as On\-Demand Instances to handle the normal capacity and launch the task instance group as Spot Instances to handle your peak load requirements\.
<a name="emr-dev-when-use-spot-cost-driven"></a>
**Cost\-Driven Workloads**  
If you are running transient clusters for which lower cost is more important than the time to completion, and losing partial work is acceptable, you can run the entire cluster \(master, core, and task instance groups\) as Spot Instances to benefit from the largest cost savings\.
<a name="emr-dev-when-use-spot-data-critical"></a>
**Data\-Critical Workloads**  
If you are running a cluster for which lower cost is more important than time to completion, but losing partial work is not acceptable, launch the master and core instance groups as on\-demand and supplement with one or more task instance groups of Spot Instances\. Running the master and core instance groups as on\-demand ensures that your data is persisted in HDFS and that the cluster is protected from termination due to Spot market fluctuations, while providing cost savings that accrue from running the task instance groups as Spot Instances\.
<a name="emr-dev-when-use-spot-application-testing"></a>
**Application Testing**  
When you are testing a new application in order to prepare it for launch in a production environment, you can run the entire cluster \(master, core, and task instance groups\) as Spot Instances to reduce your testing costs\.

### Choose What to Launch as Spot Instances<a name="ChoosingWhattoLaunchasSpotInstances"></a>

When you launch a cluster in Amazon EMR, you can choose to launch any or all of the instance groups \(master, core, and task\) as Spot Instances\. Because each type of instance group plays a different role in the cluster, the implications of launching each instance group as Spot Instances vary\. 

When you launch an instance group either as on\-demand or as Spot Instances, you cannot change its classification while the cluster is running\. To change an On\-Demand instance group to Spot Instances or vice versa, you must terminate the cluster and launch a new one\. 

The following table shows launch configurations for using Spot Instances in various applications\. 


| Project | Master Instance Group | Core Instance Group | Task Instance Groups | 
| --- | --- | --- | --- | 
| Long\-running clusters | On\-Demand | On\-Demand or instance\-fleet mix | Spot or instance\-fleet mix | 
| Cost\-driven workloads | Spot | Spot | Spot | 
| Data\-critical workloads | On\-Demand | On\-Demand | Spot or instance\-fleet mix | 
| Application testing | Spot | Spot | Spot | 

#### Master Node as Spot Instance<a name="emr-dev-master-instance-group-spot"></a>

The master node controls and directs the cluster\. When it terminates, the cluster ends, so you should only launch the master node as a Spot Instance if you are running a cluster where sudden termination is acceptable\. This might be the case if you are testing a new application, have a cluster that periodically persists data to an external store such as Amazon S3, or are running a cluster where cost is more important than ensuring the cluster’s completion\. 

When you launch the master instance group as a Spot Instance, the cluster does not start until that Spot Instance request is fulfilled\. This is something to consider when selecting your maximum Spot price\. 

You can only add a Spot Instance master node when you launch the cluster\. Master nodes cannot be added or removed from a running cluster\. 

Typically, you would only run the master node as a Spot Instance if you are running the entire cluster \(all instance groups\) as Spot Instances\. 

#### Core Instance Group as Spot Instances<a name="emr-dev-core-instance-group-spot"></a>

Core nodes process data and store information using HDFS\. You typically only run core nodes as Spot Instances if you are either not running task nodes or running task nodes as Spot Instances\. 

When you launch the core instance group as Spot Instances, Amazon EMR waits until it can provision all of the requested core instances before launching the instance group\. This means that if you request a core instance group with six nodes, the instance group does not launch if there are only five nodes available at or below your maximum Spot price\. In this case, Amazon EMR continues to wait until all six core nodes are available at your Spot price or until you terminate the cluster\. 

You can add Spot Instance core nodes either when you launch the cluster or later to add capacity to a running cluster\. You cannot shrink the size of the core instance group in a running cluster by reducing the instance count\. However, it is possible to terminate an instance in the core instance group using the AWS CLI or the API\. This should be done with caution\. Terminating an instance in the core instance group risks data loss, and the instance is not automatically replaced\. 

#### Task Instance Groups as Spot Instances<a name="emr-dev-task-instance-group-spot"></a>

The task nodes process data but do not hold persistent data in HDFS\. If they terminate because the Spot price has risen above your maximum Spot price, no data is lost and the effect on your cluster is minimal\. 

When you launch one or more task instance groups as Spot Instances, Amazon EMR provisions as many task nodes as it can, using your maximum Spot price\. This means that if you request a task instance group with six nodes, and only five Spot Instances are available at or below your maximum Spot price, Amazon EMR launches the instance group with five nodes, adding the sixth later if possible\. 

Launching task instance groups as Spot Instances is a strategic way to expand the capacity of your cluster while minimizing costs\. If you launch your master and core instance groups as On\-Demand Instances, their capacity is guaranteed for the run of the cluster\. You can add task instances to your task instance groups as needed, to handle peak traffic or speed up data processing\. 

You can add or remove task nodes using the console, AWS CLI, or API\. You can also add additional task groups, but you cannot remove a task group after it is created\. 

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