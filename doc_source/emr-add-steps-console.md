# Adding steps to a cluster using the console<a name="emr-add-steps-console"></a>

Use the following procedures to add steps to a cluster using the AWS Management Console\. For detailed information about how to submit steps for specific big data applications, see the *[Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)*\.

## Add steps during cluster creation<a name="emr-add-steps-console-cluster-creation"></a>

Using the AWS Management Console, you can add steps to a cluster when the cluster is created\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), choose **Create Cluster \- Advanced Options**\. 

1. On the **Step 1: Software and Steps** page, for **Steps \(optional\)**, select **Run multiple steps in parallel to improve cluster utilization and save cost**\. The default value for the concurrency level is 10\. You can choose between 2 and 256 steps that can run in parallel\. 
**Note**  
Running multiple steps in parallel is only supported with Amazon EMR version 5\.28\.0 and later\. 

1. For **After last step completes**, choose **Cluster enters waiting state** or **Auto\-terminate the cluster**\.

1. Choose **Step type**, then **Add step**\.

1. Type appropriate values in the fields in the **Add Step** dialog\. Options differ depending on the step type\. If you have enabled **Run multiple steps in parallel to improve cluster utilization and save cost**, the only available option for **Action on failure** is **Continue**\. Next, choose **Add**\.

## Add steps to a running cluster<a name="emr-add-steps-console-running-cluster"></a>

Using the AWS Management Console, you can add steps to a long\-running cluster \- that is, a cluster with the auto\-terminate option disabled\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, select the link for your cluster\. 

1. On the **Cluster Details** page, choose the **Steps** tab\. 

1. On the **Steps** tab, choose **Add step**\. 

1. Type appropriate values in the fields in the **Add Step** dialog, and then choose **Add**\. The options differ depending on the step type\. 

## Modify the step concurrency level in a running cluster<a name="emr-add-steps-console-modify-concurrency"></a>

Using the AWS Management Console, you can modify the step concurrency level in a running cluster\. 

**Note**  
Running multiple steps in parallel is only supported with Amazon EMR version 5\.28\.0 and later\. 

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, select the link for your cluster\. 

1. On the **Cluster Details** page, choose the **Steps** tab\. 

1. For **Concurrency**, choose **Change**\. Select a new value for the step concurrency level and then save\. 

## Add step arguments<a name="emr-add-steps-console-arguments"></a>

When you add a step to your cluster using the AWS Management Console, you can specify arguments for that step in the **Arguments** field\. You must separate arguments with whitespace and surround string arguments that consist of characters *and* whitespace with quotation marks\.

**Example : Correct arguments**  
The following example arguments are formatted correctly for the AWS Management Console, with quotation marks around the final string argument\.  

```
bash -c "aws s3 cp s3://DOC-EXAMPLE-BUCKET/my-script.sh ."
```
You can also put each argument on a separate line for readability like the following example\.  

```
bash 
-c 
"aws s3 cp s3://DOC-EXAMPLE-BUCKET/my-script.sh ."
```

**Example : Incorrect arguments**  
The following example arguments are improperly formatted for the AWS Management Console\. Notice that the final string argument, `aws s3 cp s3://DOC-EXAMPLE-BUCKET/my-script.sh .`, contains whitespace and is not surrounded by quotation marks\.  

```
bash -c aws s3 cp s3://DOC-EXAMPLE-BUCKET/my-script.sh .
```