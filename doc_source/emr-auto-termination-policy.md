# Using an auto\-termination policy<a name="emr-auto-termination-policy"></a>

An auto\-termination policy lets you orchestrate cluster cleanup without the need to monitor and manually terminate unused clusters\. When you add an auto\-termination policy to a cluster, you specify the amount of idle time after which the cluster should automatically shut down\. 

Depending on release version, Amazon EMR uses different criteria to mark a cluster as idle\. The following table outlines how Amazon EMR determines cluster idleness\.


****  

| When you use \.\.\. | A cluster is considered idle when \.\.\. | 
| --- | --- | 
| Amazon EMR versions 6\.4\.0 and later |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-auto-termination-policy.html)  | 
| Amazon EMR versions 5\.30\.0 \- 5\.33\.0 and 6\.1\.0 \- 6\.3\.0 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-auto-termination-policy.html)  Amazon EMR marks a cluster as idle and may automatically terminate the cluster even if you have an active Python3 kernel\. This is because executing a Python3 kernel does not submit a Spark job on the cluster\. To use auto\-termination with a Python3 kernel, we recommend that you use Amazon EMR version 6\.4\.0 or later\.   | 

**Note**  
Amazon EMR versions 6\.4\.0 and later support an on\-cluster file for detecting activity on the master node: `/emr/metricscollector/isbusy`\. When you use a cluster to run shell scripts or non\-YARN applications, you can periodically touch or update `isbusy` to tell Amazon EMR that the cluster is not idle\.

You can attach an auto\-termination policy when you create a cluster, or add a policy to an existing cluster\. To change or disable auto\-termination, you can update or remove the policy\.

## Considerations<a name="emr-auto-termination-considerations"></a>

Consider the following features and limitations before using an auto\-termination policy:
+ Auto\-termination is supported with Amazon EMR versions 5\.30\.0 and 6\.1\.0 and later\. 
+ Auto\-termination is available in the following AWS Regions: US East \(N\. Virginia, Ohio\), US West \(N\. California, Oregon\), Asia Pacific \(Mumbai, Seoul, Singapore, Sydney, Tokyo\), Canada \(Central\), China \(Beijing, Ningxia\), Europe \(Ireland, Frankfurt, London, Paris, Stockholm\), South America \(São Paulo\)\.
+ Idle timeout defaults to 60 minutes \(one hour\) when you don't specify an amount\. You can specify a minimum idle timeout of one minute, and a maximum idle timeout of 7 days\.
+ With Amazon EMR versions 6\.4\.0 and later, auto\-termination is enabled by default when you create a new cluster using the Amazon EMR console\.
+ Amazon EMR publishes high\-resolution Amazon CloudWatch metrics when you enable auto\-termination for a cluster\. You can use these metrics to track cluster activity and idleness\. For more information, see [Cluster capacity metrics](UsingEMR_ViewingMetrics.md#emr-metrics-managed-scaling)\.
+ Auto\-termination is not supported when you use non\-YARN based applications such as Presto, Trino, or HBase\.

## Permissions to use auto\-termination<a name="emr-auto-termination-permissions"></a>

Before you can apply and manage auto\-termination policies for Amazon EMR, you need the permissions that are listed in the following example IAM permissions policy\.

```
{
  "Version": "2012-10-17",
  "Statement": {
      "Sid": "AllowAutoTerminationPolicyActions",
      "Effect": "Allow",
      "Action": [
        "elasticmapreduce:PutAutoTerminationPolicy",
        "elasticmapreduce:GetAutoTerminationPolicy",
        "elasticmapreduce:RemoveAutoTerminationPolicy"
      ],
      "Resource": "<your-resources>"
    }
}
```

## Attach, update, or remove an auto\-termination policy<a name="emr-auto-termination-attach"></a>

This section includes instructions to help you attach, update, or remove an auto\-termination policy from an Amazon EMR cluster\. Before you work with auto\-termination policies, make sure you have the necessary IAM permissions\. See [Permissions to use auto\-termination](#emr-auto-termination-permissions)\.

------
#### [ Console ]

**To attach an auto\-termination policy when you create a cluster**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Under **Hardware configuration**, select **Auto\-termination**\.

1. Specify the number of idle hours and minutes after which the cluster should auto\-terminate\. The default idle time is one hour\.

1. Choose other settings as appropriate for your application, and then choose **Create cluster**\.

**To attach an auto\-termination policy when you create an EMR notebook**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Notebooks**, then choose **Create notebook**\.

1. Choose **Create a cluster**\.

1. Select **Enable auto\-termination**, then specify the number of idle hours and minutes after which the cluster should auto\-terminate\. The default idle time is one hour\.

1. Choose other settings as appropriate for your EMR notebook, and then choose **Create notebook**\.

**To attach, update, or remove an auto\-termination policy on a running cluster**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Select **Clusters** and choose the cluster you want to update\.

1. Choose the **Hardware** tab on the cluster detail page\.

1. Select or clear **Enable auto\-termination** to enable or disable the feature\. If you enable auto\-termination, specify the number of idle hours and minutes after which the cluster should auto\-terminate\.

------
#### [ AWS CLI ]

**Before you start**

Before you work with auto\-termination policies, we recommend that you update to the latest version of the AWS CLI\. For instructions, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\.

**To attach or update an auto\-termination policy using the AWS CLI**
+ You can use the `aws emr put-auto-termination-policy` command to attach or update an auto\-termination policy on a cluster\. 

  The following example specifies 3600 seconds for *IdleTimeout*\. If you don't specify *IdleTimeout*, the value defaults to one hour\. 

  ```
  aws emr put-auto-termination-policy \
  --cluster-id <your-cluster-id> \
  --auto-termination-policy IdleTimeout=3600
  ```
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  You can also specify a value for `--auto-termination-policy` when you use the `aws emr create-cluster` command\. For more information on using Amazon EMR commands in the AWS CLI, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**To remove an auto\-termination policy using the AWS CLI**
+ Use the `aws emr remove-auto-termination-policy` command to remove an auto\-termination policy from a cluster\. For more information on using Amazon EMR commands in the AWS CLI, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

  ```
  aws emr remove-auto-termination-policy --cluster-id <your-cluster-id>
  ```

------