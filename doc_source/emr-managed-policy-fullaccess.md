# IAM Managed Policy for Full Access \(On Path to Deprecation\)<a name="emr-managed-policy-fullaccess"></a>

The `AmazonElasticMapReduceFullAccess` managed policy grants all the required actions for Amazon EMR and other services\. The `AmazonElasticMapReduceFullAccess` managed policy is on the path to deprecation, but not yet deprecated\. When the policy is eventually deprecated, you will not abe able to attach it to a role\. However, a role that already has an attached deprecated policy can still be attached to a cluster\. `AmazonElasticMapReduceFullAccess` has been replaced with [`AmazonEMRFullAccessPolicy_v2`](emr-managed-policy-fullaccess-v2.md) as the Amazon EMR default managed policy\.

The Amazon EMR full permissions default managed policy \(`AmazonEMRFullAccessPolicy_v2`\) and Amazon EMR service policy \(`AmazonEMRServicePolicy_v2`\) are available to replace the soon\-to\-be\-deprecated policy\. The v2 policies incorporate new iam:PassRole security configurations, including the following:
+ `iam:PassRole` permissions only for specific default Amazon EMR roles\.
+ `iam:PassedToService` conditions that allow you to use the policy with only specified AWS services, such as `elasticmapreduce.amazonaws.com` and `ec2.amazonaws.com`\.

You can view the JSON version of the [AmazonEMRFullAccessPolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRFullAccessPolicy_v2) and [AmazonEMRServicePolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRServicePolicy_v2) policies in the IAM console\.

We recommend that you create new clusters using v2 managed policies\.

Following are the contents of Version 7 of this policy\. You can also use the AWS Management Console link [https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonElasticMapReduceFullAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonElasticMapReduceFullAccess) to view the policy\.

**Note**  
The `ec2:TerminateInstances` action enables the IAM user or role that assumes this policy to terminate any of the Amazon EC2 instances associated with the IAM account, even those that are not part of an Amazon EMR cluster\.

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
                "sdb:*"
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