# Amazon EMR Managed Policies<a name="emr-managed-iam-policies"></a>

The easiest way to grant full access or read\-only access to required Amazon EMR actions is to use the IAM managed policies for Amazon EMR\. Managed policies offer the benefit of updating automatically if permission requirements change\. If you use inline policies, service changes may occur that cause permission errors to appear\.

These policies not only include actions for Amazon EMR; they also include actions for Amazon EC2, Amazon S3, and Amazon CloudWatch, which Amazon EMR uses to perform actions like launching instances, writing log files, and managing Hadoop jobs and tasks\.

Cluster users require permissions for applications to pass the service roles for Amazon EMR on their behalf\. The `AmazonElasticMapReduceFullAccess` permissions policy, which is the default managed policy for users to have full permissions for Amazon EMR, includes a statement that allows the `iam:PassRole` permissions for all resources\. This statement allows the user to pass any role to other AWS services so that Amazon EMR can interact with those services on behalf of the user\.

To implement a more restrictive policy, attach an inline policy to appropriate users or groups that allows `iam:PassRole` only for roles specific to Amazon EMR\. The following example demonstrates a statement that allows `iam:PassRole` permissions only for the default Amazon EMR roles: `EMR_DefaultRole`, `EMR_EC2_DefaultRole`, and `EMR_AutoScalingDefaultRole`\. If you use custom roles, replace the default role names with your custom role names\.

```
{
    "Action": "iam:PassRole",
    "Effect": "Allow",
    "Resource": [
        "arn:aws:iam::*:role/EMR_DefaultRole",
        "arn:aws:iam::*:role/EMR_EC2_DefaultRole",
        "arn:aws:iam::*:role/EMR_AutoScaling_DefaultRole",
        "arn:aws:iam::*:role/EMR_Notebooks_DefaultRole"
    ]
}
```

To create custom policies, we recommend that you begin with the managed policies and edit them according to your requirements\.

For information about how to attach policies to IAM users \(principals\), see [Working with Managed Policies Using the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#policies_using-managed-console)in the *IAM User Guide*\.

## IAM Managed Policy for Full Access<a name="emr-managed-policy-fullaccess"></a>

To grant all the required actions for Amazon EMR, attach the `AmazonElasticMapReduceFullAccess` managed policy\. The content of this policy statement is shown below\. It reveals all the actions that Amazon EMR requires for other services\. 

The contents of Version 6 of this policy are shown below\. Because the **AmazonElasticMapReduceFullAccess** policy is updated automatically, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "cloudwatch:*",
                "cloudformation:CreateStack",
                "cloudformation:DescribeStackEvents",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:CancelSpotInstanceRequests",
                "ec2:CreateRoute",
                "ec2:CreateSecurityGroup",
                "ec2:CreateTags",
                "ec2:DeleteRoute",
                "ec2:DeleteTags",
                "ec2:DeleteSecurityGroup",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeInstances",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSpotInstanceRequests",
                "ec2:DescribeSpotPriceHistory",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs",
                "ec2:DescribeRouteTables",
                "ec2:DescribeNetworkAcls",
                "ec2:CreateVpcEndpoint",
                "ec2:ModifyImageAttribute",
                "ec2:ModifyInstanceAttribute",
                "ec2:RequestSpotInstances",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:RunInstances",
                "ec2:TerminateInstances",
                "elasticmapreduce:*",
                "iam:GetPolicy",
                "iam:GetPolicyVersion",
                "iam:ListRoles",
                "iam:PassRole",
                "kms:List*",
                "s3:*",
                "sdb:*",
                "support:CreateCase",
                "support:DescribeServices",
                "support:DescribeSeverityLevels"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": [
                        "elasticmapreduce.amazonaws.com",
                        "elasticmapreduce.amazonaws.com.cn"
                    ]
                }
            }
        }
    ]
}
```

**Note**  
The `ec2:TerminateInstances` action enables the IAM user to terminate any of the Amazon EC2 instances associated with the IAM account, even those that are not part of an EMR cluster\.

## IAM Managed Policy for Read\-Only Access<a name="emr-managed-policy-readonly"></a>

To grant read\-only privileges to Amazon EMR, attach the **AmazonElasticMapReduceReadOnlyAccess** managed policy\. The content of this policy statement is shown below\. Wildcard characters for the `elasticmapreduce` element specify that only actions that begin with the specified strings are allowed\. Keep in mind that because this policy does not explicitly deny actions, a different policy statement may still be used to grant access to specified actions\.

**Note**  
Because the **AmazonElasticMapReduceReadOnlyAccess** policy is updated automatically, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "elasticmapreduce:Describe*",
 8.         "elasticmapreduce:List*",
 9.         "elasticmapreduce:ViewEventsFromAllClustersInConsole"
10.         "s3:GetObject",
11.         "s3:ListAllMyBuckets",
12.         "s3:ListBucket",
13.         "sdb:Select",
14.         "cloudwatch:GetMetricStatistics"
15.       ],
16.       "Resource": "*"
17.     }
18.   ]
19. }
```