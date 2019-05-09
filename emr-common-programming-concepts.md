# Common Concepts for API Calls<a name="emr-common-programming-concepts"></a>

**Topics**
+ [Endpoints for Amazon EMR](#endpoints)
+ [Specifying Cluster Parameters in Amazon EMR](#API_SpecifyingParameters)
+ [Availability Zones in Amazon EMR](#AvailabilityZones)
+ [How to Use Additional Files and Libraries in Amazon EMR Clusters](#HowtoUseAdditionalFilesandLibrariesWiththeMapperorReducer)

 When you write an application that calls the Amazon EMR API, there are several concepts that apply when calling one of the wrapper functions of an SDK\. 

## Endpoints for Amazon EMR<a name="endpoints"></a>

An endpoint is a URL that is the entry point for a web service\. Every web service request must contain an endpoint\. The endpoint specifies the AWS region where clusters are created, described, or terminated\. It has the form `elasticmapreduce.regionname.amazonaws.com`\. If you specify the general endpoint \(`elasticmapreduce.amazonaws.com`\), Amazon EMR directs your request to an endpoint in the default region\. For accounts created on or after March 8, 2013, the default region is us\-west\-2; for older accounts, the default region is us\-east\-1\.

For more information about the endpoints for Amazon EMR, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#emr_region) in the *Amazon Web Services General Reference*\.

## Specifying Cluster Parameters in Amazon EMR<a name="API_SpecifyingParameters"></a>

 The `Instances` parameters enable you to configure the type and number of EC2 instances to create nodes to process the data\. Hadoop spreads the processing of the data across multiple cluster nodes\. The master node is responsible for keeping track of the health of the core and task nodes and polling the nodes for job result status\. The core and task nodes do the actual processing of the data\. If you have a single\-node cluster, the node serves as both the master and a core node\. 

 The `KeepJobAlive` parameter in a `RunJobFlow` request determines whether to terminate the cluster when it runs out of cluster steps to execute\. Set this value to `False` when you know that the cluster is running as expected\. When you are troubleshooting the job flow and adding steps while the cluster execution is suspended, set the value to `True`\. This reduces the amount of time and expense of uploading the results to Amazon Simple Storage Service \(Amazon S3\), only to repeat the process after modifying a step to restart the cluster\. 

If `KeepJobAlive` is `true`, after successfully getting the cluster to complete its work, you must send a `TerminateJobFlows` request or the cluster continues to run and generate AWS charges\. 

 For more information about parameters that are unique to `RunJobFlow`, see [RunJobFlow](https://docs.aws.amazon.com/ElasticMapReduce/latest/API//API_RunJobFlow.html)\. For more information about the generic parameters in the request, see [Common Request Parameters](https://docs.aws.amazon.com/ElasticMapReduce/latest/API//CommonParameters.html)\. 

## Availability Zones in Amazon EMR<a name="AvailabilityZones"></a>

 Amazon EMR uses EC2 instances as nodes to process clusters\. These EC2 instances have locations composed of Availability Zones and regions\. Regions are dispersed and located in separate geographic areas\. Availability Zones are distinct locations within a region insulated from failures in other Availability Zones\. Each Availability Zone provides inexpensive, low\-latency network connectivity to other Availability Zones in the same region\. For a list of the regions and endpoints for Amazon EMR, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#emr_region) in the *Amazon Web Services General Reference*\. 

 The `AvailabilityZone` parameter specifies the general location of the cluster\. This parameter is optional and, in general, we discourage its use\. When `AvailabilityZone` is not specified Amazon EMR automatically picks the best `AvailabilityZone` value for the cluster\. You might find this parameter useful if you want to colocate your instances with other existing running instances, and your cluster needs to read or write data from those instances\. For more information, see the [Amazon EC2 User Guide for Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)\. 

## How to Use Additional Files and Libraries in Amazon EMR Clusters<a name="HowtoUseAdditionalFilesandLibrariesWiththeMapperorReducer"></a>

There are times when you might like to use additional files or custom libraries with your mapper or reducer applications\. For example, you might like to use a library that converts a PDF file into plain text\. 

**To cache a file for the mapper or reducer to use when using Hadoop streaming**
+ In the JAR `args` field:, add the following argument:

  ```
  1. -cacheFile s3://bucket/path_to_executable#local_path
  ```

  The file, `local_path`, is in the working directory of the mapper, which could reference the file\.