# Service role for cluster EC2 instances \(EC2 instance profile\)<a name="emr-iam-role-for-ec2"></a>

The service role for cluster EC2 instances \(also called the EC2 instance profile for Amazon EMR\) is a special type of service role that is assigned to every EC2 instance in an Amazon EMR cluster when the instance launches\. Application processes that run on top of the Hadoop ecosystem assume this role for permissions to interact with other AWS services\.

For more information about service roles for EC2 instances, see [Using an IAM role to grant permissions to applications running on Amazon EC2 instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\.

**Important**  
The default service role for cluster EC2 instances and its associated AWS default managed policy, `AmazonElasticMapReduceforEC2Role` are on the path to deprecation, with no replacement AWS managed policies provided\. You'll need to create and specify an instance profile to replace the deprecated role and default policy\.

## Default role and managed policy<a name="emr-ec2-role-default"></a>
+ The default role name is `EMR_EC2_DefaultRole`\.
+ The `EMR_EC2_DefaultRole` default managed policy, `AmazonElasticMapReduceforEC2Role`, is on the path to deprecation and will not be replaced with another default managed policy\. Instead of using a default managed policy for the EC2 instance profile, apply resource\-based policies to S3 buckets and other resources that Amazon EMR needs, or use your own customer managed policy with an IAM role as an instance profile\. For more information, see [Creating a service role for cluster EC2 instances with least\-privilege permissions](#emr-ec2-role-least-privilege)

The following shows the contents of version 3 of `AmazonElasticMapReduceforEC2Role`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": "*",
            "Action": [
                "cloudwatch:*",
                "dynamodb:*",
                "ec2:Describe*",
                "elasticmapreduce:Describe*",
                "elasticmapreduce:ListBootstrapActions",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ListInstances",
                "elasticmapreduce:ListSteps",
                "kinesis:CreateStream",
                "kinesis:DeleteStream",
                "kinesis:DescribeStream",
                "kinesis:GetRecords",
                "kinesis:GetShardIterator",
                "kinesis:MergeShards",
                "kinesis:PutRecord",
                "kinesis:SplitShard",
                "rds:Describe*",
                "s3:*",
                "sdb:*",
                "sns:*",
                "sqs:*",
                "glue:CreateDatabase",
                "glue:UpdateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:CreateTable",
                "glue:UpdateTable",
                "glue:DeleteTable",
                "glue:GetTable",
                "glue:GetTables",
                "glue:GetTableVersions",
                "glue:CreatePartition",
                "glue:BatchCreatePartition",
                "glue:UpdatePartition",
                "glue:DeletePartition",
                "glue:BatchDeletePartition",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:BatchGetPartition",
                "glue:CreateUserDefinedFunction",
                "glue:UpdateUserDefinedFunction",
                "glue:DeleteUserDefinedFunction",
                "glue:GetUserDefinedFunction",
                "glue:GetUserDefinedFunctions"
            ]
        }
    ]
}
```

## Creating a service role for cluster EC2 instances with least\-privilege permissions<a name="emr-ec2-role-least-privilege"></a>

As a best practice, we strongly recommend that you create a service role for cluster EC2 instances and permissions policy that has the minimum permissions to other AWS services required by your application\.

The default managed policy, `AmazonElasticMapReduceforEC2Role`, provides permissions that make it easy to launch an initial cluster\. However, `AmazonElasticMapReduceforEC2Role` is on the path to deprecation and Amazon EMR will not provide a replacement AWS managed default policy for the deprecated role\. To launch an initial cluster, you need to provide a customer managed resource\-based or ID\-based policy\.

The following policy statements provide examples of the permissions required for different features of Amazon EMR\. We recommend that you use these permissions to create a permissions policy that restricts access to only those features and resources that your cluster requires\. All example policy statements use the *us\-west\-2* Region and the fictional AWS account ID *123456789012*\. Replace these as appropriate for your cluster\.

For more information about creating and specifying custom roles, see [Customize IAM roles](emr-iam-roles-custom.md)\.

**Note**  
If you create a custom EMR role for EC2, follow the basic work flow, which automatically creates an instance profile of the same name\. Amazon EC2 allows you to create instance profiles and roles with different names, but Amazon EMR does not support this configuration, and it results in an "invalid instance profile" error when you create the cluster\. 

### Reading and writing data to Amazon S3 using EMRFS<a name="emr-ec2-role-EMRFS"></a>

When an application running on an Amazon EMR cluster references data using the `s3://mydata` format, Amazon EMR uses the EC2 instance profile to make the request\. Clusters typically read and write data to Amazon S3 in this way, and Amazon EMR uses the permissions attached to the service role for cluster EC2 instances by default\. For more information, see [Configure IAM roles for EMRFS requests to Amazon S3](emr-emrfs-iam-roles.md)\.

Because IAM roles for EMRFS will fall back to the permissions attached to the service role for cluster EC2 instances, as a best practice, we recommend that you use IAM roles for EMRFS, and limit the EMRFS and Amazon S3 permissions attached to the service role for cluster EC2 instances\.

The sample statement below demonstrates the permissions that EMRFS requires to make requests to Amazon S3\.
+ *my\-data\-bucket\-in\-s3\-for\-emrfs\-reads\-and\-writes* specifies the bucket in Amazon S3 where the cluster reads and writes data and all sub\-folders using */\**\. Add only those buckets and folders that your application requires\.
+ The policy statement that allows `dynamodb` actions is required only if EMRFS consistent view is enabled\. *EmrFSMetadata* specifies the default folder for EMRFS consistent view\. For more information, see [Enable consistent view](enable-consistent-view.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:CreateBucket",
                "s3:DeleteObject",
                "s3:GetBucketVersioning",
                "s3:GetObject",
                "s3:GetObjectTagging",
                "s3:GetObjectVersion",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads",
                "s3:ListBucketVersions",
                "s3:ListMultipartUploadParts",
                "s3:PutBucketVersioning",
                "s3:PutObject",
                "s3:PutObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::my-data-bucket-in-s3-for-emrfs-reads-and-writes",
                "arn:aws:s3:::my-data-bucket-in-s3-for-emrfs-reads-and-writes/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:CreateTable",
                "dynamodb:BatchGetItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:PutItem",
                "dynamodb:DescribeTable",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:Scan",
                "dynamodb:Query",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteTable",
                "dynamodb:UpdateTable"
            ],
            "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/EmrFSMetadata"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:PutMetricData",
                "dynamodb:ListTables",
                "s3:HeadBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "sqs:GetQueueUrl",
                "sqs:DeleteMessageBatch",
                "sqs:ReceiveMessage",
                "sqs:DeleteQueue",
                "sqs:SendMessage",
                "sqs:CreateQueue"
            ],
            "Resource": "arn:aws:sqs:us-west-2:123456789012:EMRFS-Inconsistency-*"
        }
    
}
```

### Archiving log files to Amazon S3<a name="emr-ec2-role-s3-logs"></a>

The following policy statement allows the Amazon EMR cluster to archive log files to the Amazon S3 location specified\. In the example below, when the cluster was created, *s3://MyLoggingBucket/MyEMRClusterLogs* was specified using the **Log folder S3 location** in the console, using the `--log-uri` option from the AWS CLI, or using the `LogUri` parameter in the `RunJobFlow` command\. For more information, see [Archive log files to Amazon S3](emr-plan-debugging.md#emr-plan-debugging-logs-archive)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::MyLoggingBucket/MyEMRClusterLogs/*"
        }
    ]
}
```

### Using the debugging tools<a name="emr-ec2-role-debugging"></a>

The following policy statement allows actions that are required if you enable the Amazon EMR debugging tool\. Archiving log files to Amazon S3, and the associated permissions shown in the example above, are required for debugging\. For more information, see [Enable the debugging tool](emr-plan-debugging.md#emr-plan-debugging-logs-archive-debug)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sqs:GetQueueUrl",
                "sqs:SendMessage"
            ],
            "Resource": "arn:aws:sqs:us-west-2:123456789012:AWS-ElasticMapReduce-*"
        }
    ]
}
```

### Using the AWS Glue Data Catalog<a name="emr-ec2-role-glue"></a>

The following policy statement allows actions that are required if you use the AWS Glue Data Catalog as the metastore for applications\. For more information, see [Using the AWS Glue Data Catalog as the metastore for Spark SQL](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-glue.html), [Using the AWS Glue Data Catalog as the metastore for Hive](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive-metastore-glue.html), and [Using Presto with the AWS Glue Data Catalog](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-presto-glue.html) in the *Amazon EMR Release Guide*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateDatabase",
                "glue:UpdateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:CreateTable",
                "glue:UpdateTable",
                "glue:DeleteTable",
                "glue:GetTable",
                "glue:GetTables",
                "glue:GetTableVersions",
                "glue:CreatePartition",
                "glue:BatchCreatePartition",
                "glue:UpdatePartition",
                "glue:DeletePartition",
                "glue:BatchDeletePartition",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:BatchGetPartition",
                "glue:CreateUserDefinedFunction",
                "glue:UpdateUserDefinedFunction",
                "glue:DeleteUserDefinedFunction",
                "glue:GetUserDefinedFunction",
                "glue:GetUserDefinedFunctions"
            ],
            "Resource": "*",
        }
    ]
}
```