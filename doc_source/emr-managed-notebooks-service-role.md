# Service role for EMR Notebooks<a name="emr-managed-notebooks-service-role"></a>

Each EMR notebook needs permissions to access other AWS resources and perform actions\. The IAM policies attached to this service role provide permissions for the notebook to interoperate with other AWS services\. When you create a notebook using the AWS Management Console, you specify an *AWS service role*\. You can use the default role, `EMR_Notebooks_DefaultRole`, or specify a role that you create\. If a notebook has not been created before, you can choose to create the default role\.
+ The default role name is `EMR_Notebooks_DefaultRole`\.
+ The default managed policies attached to `EMR_Notebooks_DefaultRole` are `AmazonElasticMapReduceEditorsRole` and `S3FullAccessPolicy`\.

Your service role should use the following trust policy\.

**Important**  
We strongly recommend that you use the following trust policy, which includes the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition keys to limit the permissions that you give Amazon EMR to particular resources in your account\. Doing so can protect you against [the confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "elasticmapreduce.amazonaws.com"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": "<account-id>"
        },
        "ArnLike": {
          "aws:SourceArn": "arn:aws:elasticmapreduce:<region>:<account-id>:*"
        }
      }
    }
  ]
}
```

The contents of version 1 of `AmazonElasticMapReduceEditorsRole` are as follows\.

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

Following is the contents of the `S3FullAccessPolicy`\. The `S3FullAccessPolicy` allows your service role for EMR Notebooks to perform all Amazon S3 actions on objects in your AWS account\. When you create a custom service role for EMR Notebooks, you must give your service role Amazon S3 permissions\.

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

You can scope down read and write access for your service role to the Amazon S3 location where you want to save your notebook files\. Use the following minimum set of Amazon S3 permissions\.

```
"s3:PutObject",
"s3:GetObject",
"s3:GetEncryptionConfiguration",
"s3:ListBucket",
"s3:DeleteObject"
```

If your Amazon S3 bucket is encrypted, you must include the following permissions for AWS Key Management Service\.

```
"kms:Decrypt",
"kms:GenerateDataKey",
"kms:ReEncrypt",
"kms:DescribeKey"
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

## EMR Notebooks service role permissions<a name="emr-managed-notebooks-service-role-permissions"></a>

This table lists the actions that EMR Notebooks takes using the service role, along with the permissions needed for each action\.


****  

| Action | Permissions | 
| --- | --- | 
| Establish a secure network channel between a notebook and an Amazon EMR cluster, and perform necessary cleanup actions\. |  <pre>"ec2:CreateNetworkInterface", <br />"ec2:CreateNetworkInterfacePermission", <br />"ec2:DeleteNetworkInterface", <br />"ec2:DeleteNetworkInterfacePermission", <br />"ec2:DescribeNetworkInterfaces", <br />"ec2:ModifyNetworkInterfaceAttribute", <br />"ec2:AuthorizeSecurityGroupEgress", <br />"ec2:AuthorizeSecurityGroupIngress", <br />"ec2:CreateSecurityGroup",<br />"ec2:DescribeSecurityGroups", <br />"ec2:RevokeSecurityGroupEgress",<br />"ec2:DescribeTags",<br />"ec2:DescribeInstances",<br />"ec2:DescribeSubnets",<br />"ec2:DescribeVpcs",<br />"elasticmapreduce:ListInstances", <br />"elasticmapreduce:DescribeCluster", <br />"elasticmapreduce:ListSteps"</pre>  | 
| Use Git credentials stored in AWS Secrets Manager to link Git repositories to a notebook\. |  <pre>"secretsmanager:GetSecretValue"</pre>  | 
| Apply AWS tags to the network interface and default security groups that EMR Notebooks creates while setting up the secure network channel\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\. |  <pre>"ec2:CreateTags"</pre>  | 
| Access or upload notebook files and metadata to Amazon S3\. |  <pre>"s3:PutObject",<br />"s3:GetObject",<br />"s3:GetEncryptionConfiguration",<br />"s3:ListBucket",<br />"s3:DeleteObject" </pre> The following permissions are only required if you use an encrypted Amazon S3 bucket\. <pre>"kms:Decrypt",<br />"kms:GenerateDataKey",<br />"kms:ReEncrypt",<br />"kms:DescribeKey"</pre>  | 