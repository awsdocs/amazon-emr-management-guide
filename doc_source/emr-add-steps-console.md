# Adding Steps to a Cluster Using the Console<a name="emr-add-steps-console"></a>

You can add steps to a cluster using the AWS CLI, the Amazon EMR SDK, or the AWS Management Console\. 

**To add steps during cluster creation**

Using the AWS Management Console, you can add steps to a cluster when the cluster is created\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), choose **Create Cluster \- Advanced Options**\. 

1. On the **Step 1: Software and Steps** page, for **Steps \(optional\)**, select **Run multiple steps in parallel to improve cluster utilization and save cost**\. The default value for the concurrency level is 10\. You can choose between 2 and 256 steps that can run in parallel\. 
**Note**  
Running multiple steps in parallel is only supported with Amazon EMR version 5\.28\.0 and later\. 

1. For **After last step completes**, choose **Cluster enters waiting state** or **Auto\-terminate the cluster**\.

1. Choose **Step type**, then **Add step**\.

1. Type appropriate values in the fields in the **Add Step** dialog\. Options differ depending on the step type\. If you have enabled **Run multiple steps in parallel to improve cluster utilization and save cost**, the only available option for **Action on failure** is **Continue**\. Next, choose **Add**\.

**To add steps to a running cluster**

Using the AWS Management Console, you can add steps to a long\-running cluster—that is, a cluster with the auto\-terminate option disabled\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, select the link for your cluster\. 

1. On the **Cluster Details** page, choose the **Steps** tab\. 

1. On the **Steps** tab, choose **Add step**\. 

1. Type appropriate values in the fields in the **Add Step** dialog, and then choose **Add**\. The options differ depending on the step type\. 

**To modify the step concurrency level in a running cluster**

Using the AWS Management Console, you can modify the step concurrency level in a running cluster\. 
**Note**  
Running multiple steps in parallel is only supported with Amazon EMR version 5\.28\.0 and later\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, select the link for your cluster\. 

1. On the **Cluster Details** page, choose the **Steps** tab\. 

1. For **Concurrency**, choose **Change**\. Select a new value for the step concurrency level and then save\. 