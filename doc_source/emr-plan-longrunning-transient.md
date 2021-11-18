# Configuring a cluster to continue or terminate after step execution<a name="emr-plan-longrunning-transient"></a>

This topic explains the differences between using a long\-running cluster and creating a transient cluster that shuts down after the last step runs\. It also covers how to configure step execution for a cluster\.

## Create a long\-running cluster<a name="emr-long-running-clusters"></a>

By default, clusters that you create using the console or the AWS CLI are long\-running\. Long\-running clusters continue to run, accept work, and accrue charges until you take action to shut them down\.

A long\-running cluster is effective in the following situations:
+ When you need to interactively or automatically query data\.
+ When you need to interact with big data applications hosted on the cluster on an ongoing basis\.
+ When you periodically process a data set so large or so frequently that it is inefficient to launch new clusters and load data each time\.

You can also set termination protection on a long\-running cluster to avoid shutting down EC2 instances by accident or error\. For more information, see [Using termination protection](UsingEMR_TerminationProtection.md)\.

**Note**  
Amazon EMR automatically enables termination protection for all clusters with multiple master nodes, and overrides any step execution settings that you supply when you create the cluster\. You can disable termination protection after the cluster has been launched\. See [Configuring termination protection for running clusters](UsingEMR_TerminationProtection.md#emr-termination-protection-running-cluster)\. To shut down a cluster with multiple master nodes, you must first modify the cluster attributes to disable termination protection\. For instructions, see [Terminate an Amazon EMR Cluster with multiple master nodes](emr-plan-ha-launch.md#emr-plan-ha-launch-terminate)\.

## Configure a cluster to terminate after step execution<a name="emr-step-execution"></a>

When you configure termination after step execution, the cluster starts, runs bootstrap actions, and then runs the steps that you specify\. As soon as the last step completes, Amazon EMR terminates the cluster's Amazon EC2 instances\. Clusters that you launch using the Amazon EMR API have step execution enabled by default\.

Termination after step execution is effective for clusters that perform a periodic processing task, such as a daily data processing run\. Step execution also helps you ensure that you are billed only for the time required to process your data\. For more information about steps, see [Work with steps using the AWS CLI and console](emr-work-with-steps.md)\.

**To disable step execution using the Amazon EMRAPI**
+ When using the [RunJobFlow](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_RunJobFlow.html) action to create a cluster, set the [KeepJobFlowAliveWhenNoSteps](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_JobFlowInstancesConfig.html#EMR-Type-JobFlowInstancesConfig-KeepJobFlowAliveWhenNoSteps) property to `true`\.

**To enable step execution using quick options in the AWS Management Console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Step execution**\.

1. Choose other settings as appropriate for your application, and then choose **Create cluster**\.

**To enable step execution using Advanced Options in the AWS Management Console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. Under **Add steps \(optional\)** select **Auto\-terminate cluster after the last step is completed**\.

1. Choose other settings as appropriate for your application, and then choose **Create cluster**\.

**To enable step execution using the AWS CLI**
+ Specify the `--auto-terminate` parameter when you use the `create-cluster` command to create a transient cluster\.

  The following example demonstrates using the `--auto-terminate` parameter\. You can type the following command and replace *myKey* with the name of your EC2 key pair\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-5.33.0 \
  --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey \
  --steps Type=PIG,Name="Pig Program",ActionOnFailure=CONTINUE,\
  Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,\
  INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,\
  $INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/]
  --instance-type m5.xlarge --instance-count 3 --auto-terminate
  ```