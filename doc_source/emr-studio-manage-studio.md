# Manage and Monitor an Amazon EMR Studio<a name="emr-studio-manage-studio"></a>

## View Studio Details<a name="emr-studio-get-studio-id"></a>

------
#### [ Console ]

****To view details about an EMR Studio using the console****

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **EMR Studio** from the left navigation\.

1. Select your Studio from the **Studios** list to open the Studio detail page\. The Studio detail page includes **Studio setting** information, such as the Studio **Description**, **VPC**, and **Subnets**\.

------
#### [ CLI ]

**To retrieve details for an EMR Studio by Studio ID using the AWS CLI**

Use the following `describe-studio` AWS CLI command to fetch detailed information about a particular EMR Studio\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/describe-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/describe-studio.html)\.

```
aws emr describe-studio \
 --studio-id <id-of-studio-to-describe> \
```

**To retrieve a list of your EMR Studios using the AWS CLI**

Use the following `list-studios` AWS CLI command\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/list-studios.html](https://docs.aws.amazon.com/cli/latest/reference/emr/list-studios.html)\.

```
aws emr list-studios
```

The following is an example return value for the `list-studios` command in JSON format\. 

```
{
    "Studios": [
        {
            "VpcId": "vpc-b21XXXXX", 
            "Name": "example-studio-name", 
            "Url": "https://es-7HWP74SNGDXXXXXXXXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com", 
            "CreationTime": 1605672582.781, 
            "StudioId": "es-7HWP74SNGDXXXXXXXXXXXXXXX", 
            "Description": "example studio description"
        }
    ]
}
```

------

## Monitor Amazon EMR Studio Actions<a name="emr-studio-monitor"></a>

### View Amazon EMR Studio and API Activity<a name="emr-studio-cloudtrail-events"></a>

Amazon EMR Studio is integrated with AWS CloudTrail, a service that provides a record of actions taken by an IAM user, by an IAM role, or by another AWS service in Amazon EMR Studio\. CloudTrail captures API calls for EMR Studio as events, which you can view using the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\. 

EMR Studio events provide information such as which Studio or IAM user makes a request, and what kind of request it is\.

**Note**  
AWS CloudTrail events for EMR Studio are not created for on\-cluster actions such as running notebook jobs\.

You can also create a trail for continuous delivery of EMR Studio CloudTrail events to an Amazon S3 bucket\. For more information, see the *[AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)*\.

**Example CloudTrail Event: An IAM User Calls the DescribeStudio API**

The following is an example AWS CloudTrail event that is created when an IAM user, `admin`, calls the [DescribeStudio](https://docs.aws.amazon.com/emr/latest/APIReference/API_DescribeStudio.html) API\. CloudTrail records the user name as `admin`\.

**Note**  
The EMR Studio API event for DescribeStudio purposefully excludes a value for `responseElements` to protect Studio details\.

```
{
   "eventVersion":"1.08",
   "userIdentity":{
      "type":"IAMUser",
      "principalId":"AIDXXXXXXXXXXXXXXXXXX",
      "arn":"arn:aws:iam::653XXXXXXXXX:user/admin",
      "accountId":"653XXXXXXXXX",
      "accessKeyId":"AKIAIOSFODNN7EXAMPLE",
      "userName":"admin"
   },
   "eventTime":"2021-01-07T19:13:58Z",
   "eventSource":"elasticmapreduce.amazonaws.com",
   "eventName":"DescribeStudio",
   "awsRegion":"us-east-1",
   "sourceIPAddress":"72.XX.XXX.XX",
   "userAgent":"aws-cli/1.18.188 Python/3.8.5 Darwin/18.7.0 botocore/1.19.28",
   "requestParameters":{
      "studioId":"es-9O5XXXXXXXXXXXXXXXXXXXXXX"
   },
   "responseElements":null,
   "requestID":"0fxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
   "eventID":"b0xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
   "readOnly":true,
   "eventType":"AwsApiCall",
   "managementEvent":true,
   "eventCategory":"Management",
   "recipientAccountId":"653XXXXXXXXX"
}
```

### View Spark User and Job Activity<a name="emr-studio-monitor-spark-job-by-user"></a>

To view Spark job activity by Amazon EMR Studio users, you can configure user impersonation on a cluster\. With user impersonation, each Spark job that is submitted from a Workspace is associated with the Studio user who ran the code\.

When user impersonation is enabled, Amazon EMR creates an HDFS user directory on the cluster’s master node for each user that runs code in the Workspace\. For example, if user `studio-user-1@example.com` runs code, you can connect to the master node and see that `hadoop fs -ls /user` has a directory for `studio-user-1@example.com`\.

To set up Spark user impersonation, set the following properties in the following configuration classifications:
+ `core-site`
+ `livy-conf`

```
[
    {
        "Classification": "core-site",
        "Properties": {
          "hadoop.proxyuser.livy.groups": "*",
          "hadoop.proxyuser.livy.hosts": "*"
        }
    },
    {
        "Classification": "livy-conf",
        "Properties": {
          "livy.impersonation.enabled": "true"
        }
    }
]
```

To view history server pages, see [Diagnose Applications and Jobs with EMR Studio](emr-studio-debug.md)\. You can also connect to the master node of the cluster using SSH to view application web interfaces\. For more information, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\.

## Update an Amazon EMR Studio<a name="emr-studio-update-studio"></a>

After you create an EMR Studio, you can update the following attributes using the AWS CLI:
+ Name
+ Description
+ Default S3 location
+ Subnets

**To update an EMR Studio using the AWS CLI**

Use the `update-studio` AWS CLI command to update your Amazon EMR Studio\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/update-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/update-studio.html)\.

**Note**  
A Studio can have a maximum of 5 subnets, which must belong to the same VPC as the Studio\. The list of subnet IDs that you submit to the `update-studio` command can include new subnet IDs, but must also include all of the subnet IDs previously associated with the Studio\. You can't remove subnets from a Studio\.

```
aws emr update-studio \
 --studio-id <example-studio-id-to-update> \
 --name <example-new-studio-name> \
 --subnet-ids <old-subnet-id-1 old-subnet-id-2 old-subnet-id-3 new-subnet-id> \
```

To verify the changes, use the `describe-studio` AWS CLI command and specify your Studio ID\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/describe-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/describe-studio.html)\.

```
aws emr describe-studio \
 --studio-id <id-of-updated-studio> \
```

## Delete an Amazon EMR Studio<a name="emr-studio-delete-studio"></a>

------
#### [ Console ]

****To delete an EMR Studio using the console****

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **EMR Studio** from the left navigation\.

1. Select your Studio from the **Studios** list and choose **Delete**\.

------
#### [ CLI ]

**To delete an EMR Studio using the AWS CLI**

Use the `delete-studio` AWS CLI command to delete an Amazon EMR Studio\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/delete-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/delete-studio.html)\.

```
aws emr delete-studio --studio-id <id-of-studio-to-delete>
```

------

When you delete a Studio, EMR Studio deletes all of the user and group assignments associated with the Studio\. You might need to take extra steps to remove the notebook files associated with the Studio from Amazon S3\. For more information, see [Delete a Workspace and Notebook Files](emr-studio-configure-workspace.md#emr-studio-delete-workspace)\.