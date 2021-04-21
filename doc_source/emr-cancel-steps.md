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
  aws emr cancel-steps --cluster-id j-2QUAXXXXXXXXX --step-ids s-3M8DXXXXXXXXX s-3M8DXXXXXXXXX --step-cancellation-option SEND_INTERRUPT
  ```

With Amazon EMR version 5\.28\.0, you can choose one of the two following cancellation options for `StepCancellationOption` parameter when canceling steps\. 
+ `SEND_INTERRUPT`– This is the default option\. When a step cancellation request is received, EMR sends a `SIGTERM` signal to the step\. add a `SIGTERM` signal handler to your step logic to catch this signal and terminate descendant step processes or wait for them to complete\.
+ `TERMINATE_PROCESS` – When this option is selected, EMR sends a `SIGKILL` signal to the step and all its descendant processes which terminates them immediately\.

**Considerations for canceling steps**
+ Canceling a running or pending step removes that step from the active step count\.
+ Canceling a running step does not allow a pending step to start running, assuming no change to `stepConcurrencyLevel`\.
+ Canceling a running step does not trigger the step `ActionOnFailure`\.
+ For EMR 5\.32\.0 and later, `SEND_INTERRUPT StepCancellationOption` sends a `SIGTERM` signal to the step child process\. You should watch for this signal and do a cleanup and shutdown gracefully\. The `TERMINATE_PROCESS StepCancellationOption` sends a `SIGKILL` signal to the step child process and all of its descendant processes; however, asynchronous processes are not affected\.