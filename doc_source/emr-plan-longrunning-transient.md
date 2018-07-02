# Configure a Cluster to be Transient or Long\-Running<a name="emr-plan-longrunning-transient"></a>

You can run your cluster as a transient process: one that launches the cluster, loads the input data, processes the data, stores the output results, and then automatically shuts down\. This is the standard model for a cluster that performs a periodic processing task\. Shutting down the cluster automatically ensures that you are only billed for the time required to process your data\. 

The other model for running a cluster is as a long\-running cluster\. In this model, the cluster launches and loads the input data\. From there, you might interactively query the data, use the cluster as a data warehouse, or do periodic processing on a data set so large that it would be inefficient to load the data into new clusters each time\. In this model, the cluster persists even when there are no tasks queued for processing\. Another option you might want to enable on a long\-running cluster is termination protection\. This protects your cluster from being terminated accidentally or in the event that an error occurs\. For more information, see [Managing Cluster Termination](UsingEMR_TerminationProtection.md)\.

By default, clusters you create are long\-running clusters\. If you use quick create in the console or don't specify an option when using `create-cluster` from the command line or the API, auto\-terminate is disabled\. However, clusters launched using the API have auto\-terminate enabled\. You can specify auto\-termination using the console, the AWS CLI, or programmatically using the [KeepJobFlowAliveWhenNoSteps parameter](http://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_JobFlowInstancesConfig.html#EMR-Type-JobFlowInstancesConfig-KeepJobFlowAliveWhenNoSteps) while executing the [RunJobFlow](http://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_RunJobFlow.html) action\.

**To launch a transient cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. Under **Add steps \(optional\)** select **Auto\-terminate cluster after the last step is completed**\.

1. Proceed with creating the cluster as described in [Plan and Configure Clusters](emr-plan.md)\.

**To launch a transient cluster using the AWS CLI**

Specify the `--auto-terminate` parameter when you use the `create-cluster` command to create a transient cluster\.
+ The following example demonstrates using the `--auto-terminate` parameter\. You can type the following command and replace *myKey* with the name of your EC2 key pair\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-5.15.0 \
  --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey \
  --instance-type m4.large --instance-count 3 --auto-terminate
  ```

**Note**  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before you use the `create-cluster` command\.

For more information on using Amazon EMR commands in the AWS CLI, see [AWS CLI Reference](http://docs.aws.amazon.com/cli/latest/reference/emr)\.