# Service Role for EMR Notebooks<a name="emr-managed-notebooks-service-role"></a>

Each EMR notebook needs permissions to access other AWS resources and perform actions\. The IAM policies attached to this service role provide permissions for the notebook to interoperate with other AWS services\. When you create a notebook using the AWS Management Console, you specify an *AWS service role*\. You can use the default role, `EMR_Notebooks_DefaultRole`, or specify a role that you create\. If a notebook has not been created before, you can choose to create the default role\.
+ The default role is `EMR_Notebooks_DefaultRole`\.
+ The default managed policy attached to `EMR_Notebooks_DefaultRole` is `AmazonElasticMapReduceEditorsRole`\.

The contents of version 1 of `AmazonElasticMapReduceEditorsRole` are shown below\.

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

When you link Git repositories to your notebook and need to create a secret for the repository, you must add the `secretsmanager:GetSecretValue` permission in the IAM policy attached to the service role for EMR notebooks\. An example policy is demonstrated below: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
        }
    ]
}
```