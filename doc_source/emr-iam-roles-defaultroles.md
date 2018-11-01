# Use Default IAM Roles and Managed Policies<a name="emr-iam-roles-defaultroles"></a>

Amazon EMR provides the default roles listed below\. The managed policies listed are created and attached to them by default\. These roles and policies do not exist in your account until you create them\. After you create them, you can view the roles, the policies attached to them, and the permissions allowed or denied by the policies in the IAM console \([https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\. Managed policies are created and maintained by AWS, so they are updated automatically if service requirements change\. 


| Default Role | Description | Default Managed Policy | 
| --- | --- | --- | 
|  `EMR_DefaultRole`  |  This is the EMR role, which allows Amazon EMR to call other AWS services such as Amazon EC2 on your behalf\.  |  `AmazonElasticMapReduceRole`\. Requesting Spot Instances requires a service\-linked role\. If this role doesn't exist, the Amazon EMR service role must have permissions to create it or a permission error occurs\. The managed policy includes a statement to allow this action\. If you customize this role or policy, be sure to include a statement that allows the creation of this service\-linked role\. For more information, see [Default Contents of AmazonElasticMapReduceRole Permissions Policy](#emr-iam-contents-servicerole) and [Service\-Linked Role for Spot Instance Requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.   | 
|  `EMR_EC2_DefaultRole`  |  The EMR role for EC2 instances within a cluster\. Processes that run on cluster instances use this role when they call other AWS services\. For accessing EMRFS data in Amazon S3, you can specify different roles to be assumed based on the user or group making the request, or on the location of data in Amazon S3\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.  |  `AmazonElasticMapReduceforEC2Role`\. For more information, see [Default Contents of AmazonElasticMapReduceforEC2Role Permissions Policy](#emr-iam-contents-ec2role)\.  | 
|  `EMR_AutoScaling_DefaultRole`  |  Allows additional actions for dynamically scaling environments\. Required only for clusters that use automatic scaling in Amazon EMR\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.  |  `AmazonElasticMapReduceforAutoScalingRole`\. For more information, see [Default Contents of AmazonMapReduceforAutoScalingRole Permissions Policy](#emr-iam-contents-autoscalingrole)\.  | 

**To create default IAM roles for Amazon EMR in your account for the first time**

1. If you are logged in as an IAM user, make sure that you are allowed the appropriate IAM actions to create roles\. For more information, see [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)\.

1. Use the Amazon EMR console to create a cluster and leave the default roles specified\. You can do this either using **Quick options** or using **Advanced options**\. Amazon EMR automatically creates the roles when it launches the cluster\. They are available to any clusters that you launch later\.

   —OR—

   Use the `emr create-default-roles` command from the AWS CLI\. 

   The output of the command lists the contents of the roles\. The CLI configuration file \(`config`\) is populated with these role names for the `service_role` and `instance_profile` values\. For example:

   ```
   [default]
   output = json
   region = us-west-1
   aws_access_key_id = myAccessKeyID
   aws_secret_access_key = mySecretAccessKey
   emr =
        service_role = EMR_DefaultRole
        instance_profile = EMR_EC2_DefaultRole
   ```

## Managed Policy Contents<a name="emr-iam-role-contents"></a>

The contents of managed policies for Amazon EMR default roles are shown below\. Managed policies are automatically updated, so the policies shown here may be out\-of\-date\. Use the procedure below to view the most recent policy\. You can also check the version listed below against the version listed for each managed policy\.

**To view current default roles and managed policies for Amazon EMR**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose **Next** until you reach **Security options**\.

1. Choose the service role you want to view\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/role_view_default1.png)

1. Choose the permissions policy to view the policy JSON\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/role_view_default2.png)

1. Choose **Policy versions** to see the current policy version number, a history of policy updates, and the policy JSON for past versions\.

### Default Contents of AmazonElasticMapReduceRole Permissions Policy<a name="emr-iam-contents-servicerole"></a>

The contents of version 9 of this policy are shown below\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": "*",
            "Action": [
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CancelSpotInstanceRequests",
                "ec2:CreateNetworkInterface",
                "ec2:CreateSecurityGroup",
                "ec2:CreateTags",
                "ec2:DeleteNetworkInterface",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteTags",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeDhcpOptions",
                "ec2:DescribeImages",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeInstances",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribePrefixLists",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSpotInstanceRequests",
                "ec2:DescribeSpotPriceHistory",
                "ec2:DescribeSubnets",
                "ec2:DescribeTags",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcEndpoints",
                "ec2:DescribeVpcEndpointServices",
                "ec2:DescribeVpcs",
                "ec2:DetachNetworkInterface",
                "ec2:ModifyImageAttribute",
                "ec2:ModifyInstanceAttribute",
                "ec2:RequestSpotInstances",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:RunInstances",
                "ec2:TerminateInstances",
                "ec2:DeleteVolume",
                "ec2:DescribeVolumeStatus",
                "ec2:DescribeVolumes",
                "ec2:DetachVolume",
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:ListInstanceProfiles",
                "iam:ListRolePolicies",
                "iam:PassRole",
                "s3:CreateBucket",
                "s3:Get*",
                "s3:List*",
                "sdb:BatchPutAttributes",
                "sdb:Select",
                "sqs:CreateQueue",
                "sqs:Delete*",
                "sqs:GetQueue*",
                "sqs:PurgeQueue",
                "sqs:ReceiveMessage",
                "cloudwatch:PutMetricAlarm",
                "cloudwatch:DescribeAlarms",
                "cloudwatch:DeleteAlarms",
                "application-autoscaling:RegisterScalableTarget",
                "application-autoscaling:DeregisterScalableTarget",
                "application-autoscaling:PutScalingPolicy",
                "application-autoscaling:DeleteScalingPolicy",
                "application-autoscaling:Describe*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/spot.amazonaws.com/AWSServiceRoleForEC2Spot*",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": "spot.amazonaws.com"
                }
            }
        }
    ]
}
```

### Default Contents of AmazonElasticMapReduceforEC2Role Permissions Policy<a name="emr-iam-contents-ec2role"></a>

The contents of version 3 of this policy are shown below\.

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

### Default Contents of AmazonMapReduceforAutoScalingRole Permissions Policy<a name="emr-iam-contents-autoscalingrole"></a>

The contents of version 1 of this policy are shown below\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "cloudwatch:DescribeAlarms",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ModifyInstanceGroups"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```