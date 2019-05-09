# Specifying the AWS Service Role<a name="emr-managed-notebooks-service-role"></a>

Each EMR notebook needs permissions to access other AWS resources and perform actions\. The notebook's *AWS service role* defines these permissions\. The IAM policies attached to the service role provide permissions for the cluster to interoperate with other AWS services\.

The default service role for an EMR notebook is **EMR\_Notebooks\_DefaultRole**\. The default permissions policy attached to this role are in the default managed policy, **EMRNotebooksDefaultRolePolicy**\. The contents of this policy are listed as follows:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CreateSecurityGroup",
                "ec2:DescribeSecurityGroups",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:CreateNetworkInterface",
                "ec2:CreateNetworkInterfacePermission",
                "ec2:DeleteNetworkInterface",
                "ec2:DeleteNetworkInterfacePermission",
                "ec2:DescribeNetworkInterfaces",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:DescribeTags",
                "ec2:DescribeInstances",
                "ec2:DescribeSubnets",
                "elasticmapreduce:ListInstances",
                "elasticmapreduce:DescribeCluster"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "arn:aws:ec2:*:*:network-interface/*",
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:TagKeys": [
                        "aws:elasticmapreduce:editor-id",
                        "aws:elasticmapreduce:job-flow-id"
                    ]
                }
            }
        }
    ]
}
```

The default role also has the **S3FullAccessPolicy ** attached\. The contents of this policy are as follows:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```