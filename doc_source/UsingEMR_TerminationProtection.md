# Managing Cluster Termination<a name="UsingEMR_TerminationProtection"></a>

Termination protection helps ensure that the EC2 instances in your job flow are not shut down by an accident or error\. This protection is especially useful if your cluster contains data in instance storage that you need to recover before those instances are terminated\. When termination protection is not enabled, you can terminate clusters either through calls to the `TerminateJobFlows` API, through the Amazon EMR console, or by using the command line interface\. In addition, the master node may terminate a node that has become unresponsive or has returned an error\.

**Warning**  
Termination protection helps protect cluster instances from accidental shutdown, but it does not guarantee that data is retained in the event of a human error—for example, if a reboot command is issued from the command line while connected to the instance, or if termination protection is disabled and a shutdown or reboot call is made through the API, the AWS CLI, or the AWS Management Console\. When an instance shuts down, data saved to ephemeral storage on the instance, such as HDFS data, is lost and cannot be recovered\. Back up critical data to Amazon S3 and as appropriate for your business continuity requirements\.

By default, termination protection is enabled when you launch a cluster using the console\. Termination protection is disabled by default when you launch a cluster using the CLI or API\. When termination protection is enabled, you must explicitly remove termination protection from the cluster before you can terminate it\. With termination protection enabled, `TerminateJobFlows` cannot terminate the cluster and users cannot terminate the cluster using the CLI\. Users terminating the cluster using the Amazon EMR console are prompted with an extra step to turn termination protection off before terminating the cluster\. 

If you attempt to terminate a protected cluster with the API or CLI, the API returns an error, and the CLI exits with a non\-zero return code\. 

When you submit steps to a cluster, the ActionOnFailure setting determines what the cluster does in response to any errors\. The possible values for this setting are: 

+ TERMINATE\_JOB\_FLOW: If the step fails, terminate the cluster\. If the cluster has termination protection enabled AND auto\-terminate disabled, it will not terminate\.

+ CANCEL\_AND\_WAIT: If the step fails, cancel the remaining steps\. If the cluster has auto\-terminate disabled, the cluster will not terminate\.

+ CONTINUE: If the step fails, continue to the next step\. 

## Termination Protection in Amazon EMR and Amazon EC2<a name="TerminationProtectioninEMRandEC2"></a>

An EMR cluster with termination protection enabled has the `disableAPITermination` attribute set for all EC2 instances in the cluster\. If there is a conflict between the termination protection setting in Amazon EC2 and the setting in Amazon EMR for an instance, the Amazon EMR setting overrides the Amazon EC2 setting on the given instance\. For example, if you use the Amazon EC2 console to *enable* termination protection on an EC2 instance in an Amazon EMR cluster that has termination protection *disabled*, Amazon EMR turns off termination protection on that EC2 instance and shuts down the instance when the rest of the cluster terminates\. 

## Termination Protection and Spot Instances<a name="TerminationProtectionandSpotInstances"></a>

Amazon EMR termination protection does not prevent an Amazon EC2 Spot Instance from terminating when the Spot Price rises above the maximum Spot price\. 

## Termination Protection and Auto\-Terminate<a name="TerminationProtectionandKeepAlive"></a>

Enabling auto\-terminate creates a transient cluster\. The cluster automatically terminates when the last step is successfully completed even if termination protection is enabled\. 

Disabling auto\-terminate causes instances in a cluster to persist after steps have successfully completed, but still allows the cluster to be terminated by user action, by errors, and by calls to `TerminateJobFlows` \(if termination protection is disabled\)\. 

**Note**  
By default, auto\-terminate is disabled for clusters launched using the console and the CLI\. Clusters launched using the API have auto\-terminate enabled\.

## Configuring Termination Protection for New Clusters<a name="ProtectingaNewJobFlow"></a>

You can enable or disable termination protection when you launch a cluster using the console, the AWS CLI, or the API\. 

**To configure termination protection for a new cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. In the **Cluster Configuration** section, set the **Termination protection** field to **Yes** to enable protection, or set the field to **No** to disable it\. By default, termination protection is enabled\.   
![\[Termination protection setting\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/termination-protection.png)

1. Proceed with creating the cluster\.

**To configure termination protection for a new cluster using the AWS CLI**

Using the AWS CLI, you can launch a cluster with termination protection enabled by typing the `create-cluster` command with the `--termination-protected` parameter\. By default, termination protection is disabled when you launch a cluster using the AWS CLI\. You can also use the `--no-termination-protected` parameter to disable termination protection\.

+ To launch a protected cluster, type the following command and replace *myKey* with the name of your EC2 key pair\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --applications Name=Hadoop Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-type m3.xlarge --instance-count 3 --termination-protected
  ```

  For more information about using Amazon EMR commands in the AWS CLI, see [http://docs.aws.amazon.com/cli/latest/reference/emr](http://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Configuring Termination Protection for Running Clusters<a name="ProtectinganExistingJobFlow"></a>

You can configure termination protection for a running cluster using the console or the AWS CLI\. 

**To configure termination protection for a running cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Cluster List** page, choose the link for your cluster\. 

1. On the **Cluster Details** page, in the **Summary** section, for **Termination protection**, choose **Change**\.

1. To enable termination protection, choose **On** and select the check mark icon\. Alternatively, choose **Off** to disable it\.  
![\[Confirm termination protection change\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/change-termination-console.png)

**To configure termination protection for a running cluster using the AWS CLI**

To enable termination protection on a running cluster using the AWS CLI, type the `modify-cluster-attributes` subcommand with the `--termination-protected` parameter\. To disable it, type the `--no-termination-protected` parameter\.

+ Type the following command to enable termination protection on a running cluster\.

  ```
  1. aws emr modify-cluster-attributes --cluster-id j-3KVTXXXXXX7UG --termination-protected
  ```

  To disable termination protection, type:

  ```
  1. aws emr modify-cluster-attributes --cluster-id j-3KVTXXXXXX7UG --no-termination-protected
  ```