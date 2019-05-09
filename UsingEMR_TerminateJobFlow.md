# Terminate a Cluster<a name="UsingEMR_TerminateJobFlow"></a>

This section describes the methods of terminating a cluster\. For information about enabling termination protection and auto\-terminating clusters, see [Control Cluster Termination](emr-plan-termination.md)\. You can terminate clusters in the `STARTING`, `RUNNING`, or `WAITING` states\. A cluster in the `WAITING` state must be terminated or it runs indefinitely, generating charges to your account\. You can terminate a cluster that fails to leave the `STARTING` state or is unable to complete a step\. 

If you are terminating a cluster that has termination protection set on it, you must disable termination protection before you can terminate the cluster\. Clusters can be terminated using the console, the AWS CLI, or programmatically using the `TerminateJobFlows` API\.

Depending on the configuration of the cluster, it may take up to 5\-20 minutes for the cluster to completely terminate and release allocated resources, such as EC2 instances\.

## Terminate a Cluster Using the Console<a name="emr-dev-terminate-job-flow-console"></a>

You can terminate one or more clusters using the Amazon EMR console\. The steps to terminate a cluster in the console vary depending on whether termination protection is on or off\. To terminate a protected cluster, you must first disable termination protection\. 

**To terminate a cluster with termination protection off**

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Select the cluster to terminate\. You can select multiple clusters and terminate them at the same time\.

1. Choose **Terminate**\.

1. When prompted, choose **Terminate**\.

   Amazon EMR terminates the instances in the cluster and stops saving log data\.

**To terminate a cluster with termination protection on**

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Cluster List** page, select the cluster to terminate\. You can select multiple clusters and terminate them at the same time\.

1. Choose **Terminate**\.

1. When prompted, choose **Change** to turn termination protection off\. If you selected multiple clusters, choose **Turn off all** to disable termination protection for all the clusters at once\.

1. In the **Terminate clusters** dialog, for **Termination Protection**, choose **Off** and then click the check mark to confirm\.

1. Click **Terminate**\.

   Amazon EMR terminates the instances in the cluster and stops saving log data\.

## Terminate a Cluster Using the AWS CLI<a name="emr-dev-terminate-job-flow-cli"></a>

**To terminate an unprotected cluster using the AWS CLI**

To terminate an unprotected cluster using the AWS CLI, use the `terminate-clusters` subcommand with the \-\-cluster\-ids parameter\. 
+ Type the following command to terminate a single cluster and replace *j\-3KVXXXXXXX7UG* with your cluster ID\.

  ```
  1. aws emr terminate-clusters --cluster-ids j-3KVXXXXXXX7UG
  ```

  To terminate multiple clusters, type the following command and replace *j\-3KVXXXXXXX7UG* and *j\-WJ2XXXXXX8EU* with your cluster IDs\.

  ```
  1. aws emr terminate-clusters --cluster-ids j-3KVXXXXXXX7UG j-WJ2XXXXXX8EU
  ```

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**To terminate a protected cluster using the AWS CLI**

To terminate a protected cluster using the AWS CLI, first disable termination protection using the `modify-cluster-attributes` subcommand with the `--no-termination-protected` parameter\. Then use the `terminate-clusters` subcommand with the `--cluster-ids` parameter to terminate it\. 

1. Type the following command to disable termination protection and replace *j\-3KVTXXXXXX7UG* with your cluster ID\.

   ```
   1. aws emr modify-cluster-attributes --cluster-id j-3KVTXXXXXX7UG --no-termination-protected
   ```

1. To terminate the cluster, type the following command and replace *j\-3KVXXXXXXX7UG* with your cluster ID\.

   ```
   1. aws emr terminate-clusters --cluster-ids j-3KVXXXXXXX7UG
   ```

   To terminate multiple clusters, type the following command and replace *j\-3KVXXXXXXX7UG* and *j\-WJ2XXXXXX8EU* with your cluster IDs\.

   ```
   1. aws emr terminate-clusters --cluster-ids j-3KVXXXXXXX7UG j-WJ2XXXXXX8EU
   ```

   For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Terminate a Cluster Using the API<a name="emr-dev-terminate-job-flow-api"></a>

The `TerminateJobFlows` operation ends step processing, uploads any log data from Amazon EC2 to Amazon S3 \(if configured\), and terminates the Hadoop cluster\. A cluster also terminates automatically if you set `KeepJobAliveWhenNoSteps` to `False` in a `RunJobFlows` request\.

You can use this action to terminate either a single cluster or a list of clusters by their cluster IDs\.

For more information about the input parameters unique to `TerminateJobFlows`, see [ TerminateJobFlows](https://docs.aws.amazon.com/ElasticMapReduce/latest/API//API_TerminateJobFlows.html)\. For more information about the generic parameters in the request, see [Common Request Parameters](https://docs.aws.amazon.com/ElasticMapReduce/latest/API//CommonParameters.html)\.