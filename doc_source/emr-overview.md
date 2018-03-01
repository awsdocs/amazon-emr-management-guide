# Overview of Amazon EMR<a name="emr-overview"></a>

 This topic provides an overview of Amazon EMR clusters, including how to submit work to a cluster, how that data is processed, and the various states that the cluster goes through during processing\. 


+ [Understanding Clusters and Nodes](#emr-overview-clusters)
+ [Submitting Work to a Cluster](#emr-work-cluster)
+ [Processing Data](#emr-overview-data-processing)
+ [Understanding the Cluster Lifecycle](#emr-overview-cluster-lifecycle)

## Understanding Clusters and Nodes<a name="emr-overview-clusters"></a>

 The central component of Amazon EMR is the *cluster*\. A cluster is a collection of Amazon Elastic Compute Cloud \(Amazon EC2\) instances\. Each instance in the cluster is called a *node*\. Each node has a role within the cluster, referred to as the *node type*\. Amazon EMR also installs different software components on each node type, giving each node a role in a distributed application like Apache Hadoop\.

 The node types in Amazon EMR are as follows: 

+ **Master node**:  A node that manages the cluster by running software components to coordinate the distribution of data and tasks among other nodes—collectively referred to as slave nodes—for processing\. The master node tracks the status of tasks and monitors the health of the cluster\.

+ **Core node**:  A slave node with software components that run tasks and store data in the Hadoop Distributed File System \(HDFS\) on your cluster\.

+ **Task node**:  A slave node with software components that only run tasks\. Task nodes are optional\.

 The following diagram represents a cluster with one master node and four slave nodes\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-node-types.png)

## Submitting Work to a Cluster<a name="emr-work-cluster"></a>

 When you run your cluster on Amazon EMR, you have several options as to how you specify the work that needs to be done\. 

+  Provide the entire definition of the work to be done in the Map and Reduce functions\. This is typically done for clusters that process a set amount of data and then terminate when processing is complete\. For more information, see [Apache Hadoop](http://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hadoop.html) in the *Amazon EMR Release Guide*\. 

+  Create a long\-running cluster and use the Amazon EMR console, the Amazon EMR API, or the AWS CLI to submit steps, which may contain one or more Hadoop jobs\. For more information, see [Submit Work to a Cluster](AddingStepstoaJobFlow.md)\. 

+  Create a cluster with a Hadoop application, such as Hive or Pig, installed and use the interface provided by the application to submit queries, either scripted or interactively\. For more information, see the [Amazon EMR Release Guide](http://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\. 

+  Create a long\-running cluster, connect to it, and submit Hadoop jobs using the Hadoop API\. For more information, go to [Class JobClient](http://hadoop.apache.org/docs/current/api/org/apache/hadoop/mapred/JobClient.html) in the Apache Hadoop API documentation\. 

## Processing Data<a name="emr-overview-data-processing"></a>

When you launch your cluster, you choose the frameworks and applications to install for your data processing needs\. There are two ways to process data in your Amazon EMR cluster: by submitting jobs or queries directly to the applications that are installed on your cluster or by running *steps* in the cluster\.

### Submitting Jobs Directly to Applications<a name="emr-overview-submitting-jobs"></a>

You can submit jobs and interact directly with the software that is installed in your Amazon EMR cluster\. To do this, you typically connect to the master node over a secure connection and access the interfaces and tools that are available for the software that runs directly on your cluster\. For more information, see [Connect to the Cluster](emr-connect-master-node.md)\.

### Running Steps to Process Data<a name="emr-overview-steps"></a>

 You can submit one or more ordered steps to an Amazon EMR cluster\. Each step is a unit of work that contains instructions to manipulate data for processing by software installed on the cluster\.

 The following is an example process using four steps: 

1. Submit an input dataset for processing\.

1. Process the output of the first step by using a Pig program\.

1. Process a second input dataset by using a Hive program\.

1. Write an output dataset\.

 Generally, when you process data in Amazon EMR, the input is data stored as files in your chosen underlying file system, such as Amazon S3 or HDFS\. This data passes from one step to the next in the processing sequence\. The final step writes the output data to a specified location, such as an Amazon S3 bucket\.

 Steps are run in the following sequence: 

1. A request is submitted to begin processing steps\.

1. The state of all steps is set to **PENDING**\.

1. When the first step in the sequence starts, its state changes to **RUNNING**\. The other steps remain in the **PENDING** state\.

1. After the first step completes, its state changes to **COMPLETED**\.

1. The next step in the sequence starts, and its state changes to **RUNNING**\. When it completes, its state changes to **COMPLETED**\.

1. This pattern repeats for each step until they all complete and processing ends\.

The following diagram represents the step sequence and change of state for the steps as they are processed\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/step-sequence.png)

 If a step fails during processing, its state changes to **TERMINATED\_WITH\_ERRORS**\. By default, any remaining steps in the sequence are set to **CANCELLED** and do not run, although you can choose to ignore processing failures and allow remaining steps to proceed\.

The following diagram represents the step sequence and default change of state when a step fails during processing\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/step-sequence-failed.png)

## Understanding the Cluster Lifecycle<a name="emr-overview-cluster-lifecycle"></a>

 A successful Amazon EMR cluster follows this process: 

1.  Amazon EMR first provisions a cluster with your chosen applications, such as Hadoop or Spark\. During this phase, the cluster state is `STARTING`\. 

1.  Next, any user\-defined actions—called *bootstrap actions*—such as installing additional applications, run on the cluster\. During this phase, the cluster state is `BOOTSTRAPPING`\. 

1.  After all bootstrap actions are successfully completed, the cluster state is `RUNNING`\. The cluster sequentially runs all steps during this phase\. 

1.  After steps run successfully, the cluster either goes into a `WAITING` state or a `SHUTTING_DOWN` state, described as follows\. 

   +  If you configured your cluster as a long\-running cluster by disabling auto\-terminate or by using the KeepJobFlowAliveWhenNoSteps parameter in the API, the cluster will go into a `WAITING` state after processing all submitted steps and wait for the next set of instructions\. If you have more data to process, you can add more steps\. You must manually terminate a long\-running cluster when you no longer need it\. After you manually terminate the cluster, it goes into the `SHUTTING_DOWN` state and then into the `TERMINATED` state\. 

   +  If you configured your cluster as a transient cluster by enabling auto\-terminate or by using the KeepJobFlowAliveWhenNoSteps parameter in the API, it automatically goes into the `SHUTTING_DOWN` state after all of the steps complete\. The instances are terminated, and all data stored in the cluster itself is deleted\. Information stored in other locations, such as in your Amazon S3 bucket, persists\. When the shutdown process completes, the cluster state is set to `COMPLETED`\. 

A failure during the cluster process terminates the cluster and all of its instances unless: a\) you enable termination protection; or b\) you supply an ActionOnFailure in the [StepConfig](Amazon EMR API ReferenceAPI_StepConfig.html) for the step\. Any data stored on the cluster is deleted\. The cluster state is set to `TERMINATED_WITH_ERRORS`\. If you have enabled termination protection so that you can retrieve data from your cluster in the event of a failure, then the cluster is not terminated\. Once you are truly done with the cluster, you can remove termination protection and terminate the cluster\. For more information, see [Managing Cluster Termination](UsingEMR_TerminationProtection.md)\. 

 The following diagram represents the lifecycle of a cluster, and how each stage of the lifecycle maps to a particular cluster state\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-cluster-lifecycle.png)

 For more information about cluster states, see [JobFlowExecutionStatusDetail data type](http://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_JobFlowExecutionStatusDetail.html) in the *Amazon EMR API Reference*\. For more information about submitting steps and configuring the cluster lifecycle, see [Submitting Work to a Cluster](#emr-work-cluster) and [Configure a Cluster to be Transient or Long\-Running](emr-plan-longrunning-transient.md)\. 