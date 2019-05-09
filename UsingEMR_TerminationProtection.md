# Using Termination Protection<a name="UsingEMR_TerminationProtection"></a>

When termination protection is enabled on a long\-running cluster, you can still terminate the cluster, but you must explicitly remove termination protection from the cluster first\. This helps ensure that EC2 instances are not shut down by an accident or error\. Termination protection is especially useful if your cluster might have data stored on local disks that you need to recover before the instances are terminated\. You can enable termination protection when you create a cluster, and you can change the setting on a running cluster\.

With termination protection enabled, the `TerminateJobFlows` action in the Amazon EMR API does not work\. Users cannot terminate the cluster using this API or the `terminate-clusters` command from the AWS CLI\. The API returns an error, and the CLI exits with a non\-zero return code\. When using the Amazon EMR console to terminate a cluster, you are prompted with an extra step to turn termination protection off\.

**Warning**  
Termination protection does not guarantee that data is retained in the event of a human error or a workaround—for example, if a reboot command is issued from the command line while connected to the instance using SSH, if an application or script running on the instance issues a reboot command, or if the Amazon EC2 or Amazon EMR API is used to disable termination protection\. Even with termination protection enabled, data saved to instance storage, including HDFS data, can be lost\. Write data output to Amazon S3 locations and create backup strategies as appropriate for your business continuity requirements\.

Termination protection does not affect your ability to scale cluster resources using any of the following actions:
+ Resizing a cluster manually using the AWS Management Console or AWS CLI\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md)\.
+ Removing instances from a core or task instance group using a scale\-in policy with automatic scaling\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.
+ Removing instances from an instance fleet by reducing target capacity\. For more information, see [Instance Fleet Options](emr-instance-fleet.md#emr-instance-fleet-options)\.

## Termination Protection and Amazon EC2<a name="emr-termination-protection-ec2"></a>

An Amazon EMR cluster with termination protection enabled has the `disableAPITermination` attribute set for all Amazon EC2 instances in the cluster\. If a termination request originates with Amazon EMR, and the Amazon EMR and Amazon EC2 settings for an instance conflict, the Amazon EMR setting overrides the Amazon EC2 setting\. For example, if you use the Amazon EC2 console to *enable* termination protection on an Amazon EC2 instance in a cluster that has termination protection *disabled*, when you use the Amazon EMR console, AWS CLI commands for Amazon EMR, or the Amazon EMR API to terminate the cluster, Amazon EMR sets `DisableApiTermination` to `false` and terminates the instance along with other instances\.

**Important**  
If an instance is created as part of an Amazon EMR cluster with termination protection, and the Amazon EC2 API or AWS CLI commands are used to modify the instance so that `DisableApiTermination` is `false`, and then the Amazon EC2 API or AWS CLI commands execute the `TerminateInstances` action, the Amazon EC2 instance terminates\.

## Termination Protection and Unhealthy YARN Nodes<a name="emr-termination-protection-unhealthy"></a>

Amazon EMR periodically checks the Apache Hadoop YARN status of nodes running on core and task Amazon EC2 instances in a cluster\. The health status is reported by the [NodeManager Health Checker Service](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/NodeManager.html#Health_checker_service)\. If a node reports `UNHEALTHY`, the Amazon EMR instance controller blacklists the node and does not allocate YARN containers to it until it becomes healthy again\. A common reason for unhealthy nodes is that disk utilization goes above 90%\. For more information about identifying unhealthy nodes and recovering, see [Resource Errors](emr-troubleshoot-error-resource.md)\.

If the node remains `UNHEALTHY` for more than 45 minutes, Amazon EMR takes the following action based on the status of termination protection\.


| Termination Protection | Result | 
| --- | --- | 
|  Enabled \(Recommended\)  |  The Amazon EC2 instance remains in a blacklisted state and continues to count toward cluster capacity\. You can connect to the Amazon EC2 instance for configuration and data recovery, and resize your cluster to add capacity\. For more information, see [Resource Errors](emr-troubleshoot-error-resource.md)\.  | 
|  Disabled  |  The Amazon EC2 instance is terminated\. Amazon EMR provisions a new instance based on the specified number of instances in the instance group or the target capacity for instance fleets\. If all core nodes are `UNHEALTHY` for more than 45 minutes, the cluster terminates, reporting a `NO_SLAVES_LEFT` status\.  HDFS data may be lost if a core instance terminates because of an unhealthy state\. If the node stored blocks that were not replicated to other nodes, these blocks are lost, which might lead to data loss\. We recommend that you use termination protection so that you can connect to instances and recover data as necessary\.   | 

## Termination Protection, Auto\-Termination, and Step Execution<a name="emr-termination-protection-steps"></a>

The auto\-terminate setting takes precedence over termination protection\. If both are enabled, when steps finish executing, the cluster terminates instead of entering a waiting state\.

When you submit steps to a cluster, you can set the `ActionOnFailure` property to determine what happens if the step can't complete execution because of an error\. The possible values for this setting are `TERMINATE_CLUSTER` \(`TERMINATE_JOB_FLOW` with earlier versions\), `CANCEL_AND_WAIT`, and `CONTINUE`\. For more information, see [Work with Steps Using the CLI and Console](emr-work-with-steps.md)\.

If a step fails that is configured with `ActionOnFailure` set to `CANCEL_AND_WAIT`, if auto\-termination is enabled, the cluster terminates without executing subsequent steps\.

If a step fails that is configured with `ActionOnFailure` set to `TERMINATE_CLUSTER`, use the table of settings below to determine the outcome\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/UsingEMR_TerminationProtection.html)

## Termination Protection and Spot Instances<a name="emr-termination-protection-spot"></a>

Amazon EMR termination protection does not prevent an Amazon EC2 Spot Instance from terminating when the Spot Price rises above the maximum Spot price\.

## Configuring Termination Protection When You Launch a Cluster<a name="emr-termination-protection-create-cluster"></a>

You can enable or disable termination protection when you launch a cluster using the console, the AWS CLI, or the API\. 

The default termination protection setting depends on how you launch the cluster:
+ Amazon EMR Console Quick Options—Termination Protection is **disabled** by default\.
+ Amazon EMR Console Advanced Options—Termination Protection is **enabled** by default\.
+ AWS CLI `aws emr create-cluster`—Termination Protection is **disabled** unless `--termination-protected` is specified\.
+ Amazon EMR API [RunJobFlow](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_RunJobFlow) command—Termination Protection is **disabled** unless the `TerminationProtected` boolean value is set to `true`\.

**To enable or disable termination protection when creating a cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. For **Step 3: General Cluster Settings**, under **General Options** make sure **Termination protection** is selected to enable it, or clear the selection to disable it\.  
![\[Termination protection setting in advanced options for cluster creation.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/termination-protection.png)

1. Choose other settings as appropriate for your application, choose **Next**, and then finish configuring your cluster\.

**To enable termination protection when creating a cluster using the AWS CLI**
+ Using the AWS CLI, you can launch a cluster with termination protection enabled by using the `create-cluster` command with the `--termination-protected` parameter\. Termination protection is disabled by default\.

  The following example creates cluster with termination protection enabled:
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "TerminationProtectedCluster" --release-label emr-5.23.0 \
  --applications Name=Hadoop Name=Hive Name=Pig \
  --use-default-roles --ec2-attributes KeyName=myKey --instance-type m4.large \
  --instance-count 3 --termination-protected
  ```

  For more information about using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Configuring Termination Protection for Running Clusters<a name="emr-termination-protection-running-cluster"></a>

You can configure termination protection for a running cluster using the console or the AWS CLI\. 

**To enable or disable termination protection for a running cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Clusters** page, choose the **Name** of your cluster\. 

1. On the **Summary** tab, for **Termination protection**, choose **Change**\.

1. To enable termination protection, choose **On**\. To disable termination protection, choose **Off**\. Then choose the green check mark to confirm\.  
![\[Confirm termination protection change\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/change-termination-console.png)

**To enable or disable termination protection for a running cluster using the AWS CLI**
+ To enable termination protection on a running cluster using the AWS CLI, use the `modify-cluster-attributes` command with the `--termination-protected` parameter\. To disable it, use the `--no-termination-protected` parameter\.

  The following example enables termination protection on the cluster with ID *j\-3KVTXXXXXX7UG*:

  ```
  1. aws emr modify-cluster-attributes --cluster-id j-3KVTXXXXXX7UG --termination-protected
  ```

  The following example disables termination protection on the same cluster:

  ```
  1. aws emr modify-cluster-attributes --cluster-id j-3KVTXXXXXX7UG --no-termination-protected
  ```