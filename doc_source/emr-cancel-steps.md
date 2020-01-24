# Canceling Steps<a name="emr-cancel-steps"></a>

You can cancel pending and running steps using the AWS Management Console, the AWS CLI, or the Amazon EMR API\.

**To cancel steps using the AWS Management Console**

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, choose the link for the cluster\. 

1. On the **Cluster Details** page, expand the **Steps** section\.

1. For each step you want to cancel, select the step from the list of **Steps**\. Then choose **Cancel step**\.

1. In the **Cancel step** dialog, keep the default option **Cancel the step and wait for it to exit**\. If you want to end the step immediately without waiting for any processes to complete, choose **Cancel the step and force it to exit**\. 

1. Choose **Cancel step**\.

**To cancel steps using the AWS CLI**
+ Use the `aws emr cancel-steps` command, specifying the cluster and steps to cancel\. The following example demonstrates an AWS CLI command to cancel two steps\.

  ```
  aws emr cancel-steps --cluster-id j-2QUAJ7T3OTEI8 --step-ids s-3M8DKCZYYN1QE,s-3M8DKCZYYN1QE
  ```

With Amazon EMR version 5\.28\.0, you can choose one of the two following cancellation options for `StepCancellationOption` parameter when canceling steps\. 
+ `SEND_INTERRUPT_SIGNAL`– This is the default option\. When a step cancellation request is received, EMR will send a SIGTERM signal to the step child process until it ends\.
+ `TERMINATE_PROCESS` – When this option is selected, EMR sends a SIGKILL signal to the step child process\. 