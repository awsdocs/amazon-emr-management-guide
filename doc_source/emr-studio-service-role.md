# Create an EMR Studio Service Role<a name="emr-studio-service-role"></a>

## About the EMR Studio Service Role<a name="emr-studio-about-service-role"></a>

Each Amazon EMR Studio uses an IAM service role with permissions that let the Studio interoperate with other AWS services\. The service role must include permissions that allow EMR Studio to establish a secure network channel between Workspaces and clusters, store notebook files in Amazon S3, and access the AWS Secrets Manager while linking a Workspace to a Git repository\. For more information about each required permission, see [EMR Studio Service Role Permissions](#emr-studio-service-role-permissions-table)\.

Use the Studio service role instead of IAM session policies to define all Amazon S3 access permissions for storing notebook files, and to define AWS Secrets Manager access permissions\.

After you create the role, you refer to it by Amazon Resource Name \(ARN\) when you create a Studio\. 

## Prerequisites<a name="emr-studio-service-role-prereqs"></a>

To create an Amazon EMR Studio service role, you need the following items:
+ A designated AWS account for your EMR Studio\. You must create your EMR Studio service role in the same account as your Studio\. If you use multiple accounts in your AWS organization, use a *member* account\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ An IAM principal in your designated AWS account that has the necessary permissions to create a service role\. For more information, see [Service role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#id_roles_create_service-permissions) in the *AWS Identity and Access Management User Guide*\.

## Instructions<a name="emr-studio-service-role-instructions"></a>

Follow the instructions in [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) to create the service role using the following trust and permissions policies\. 

**Note**  
Change `"Resource":"*"` in the following policies to specify the Amazon Resource Name \(ARN\) of the object or objects that the statement covers for your use cases\. `"Resource":"*"` is used here for example purposes only\.

Your service role must use the following trust policy\.

```
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "elasticmapreduce.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Replace any default permissions associated with the role with the following permissions policy\. 

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
                "ec2:DescribeVpcs",
                "elasticmapreduce:ListInstances",
                "elasticmapreduce:DescribeCluster",
                "elasticmapreduce:ListSteps"
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
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:GetEncryptionConfiguration",
                "s3:ListBucket",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:*"
        }
    ]
}
```

## EMR Studio Service Role Permissions<a name="emr-studio-service-role-permissions-table"></a>

This table lists the actions that EMR Studio takes using the service role, along with the permissions needed for each action\.


****  

| Action | Permissions | 
| --- | --- | 
| Establish a secure network channel between a Workspace and an EMR cluster, as well as perform necessary cleanup actions\. |  <pre>"ec2:CreateNetworkInterface", <br />"ec2:CreateNetworkInterfacePermission", <br />"ec2:DeleteNetworkInterface", <br />"ec2:DeleteNetworkInterfacePermission", <br />"ec2:DescribeNetworkInterfaces", <br />"ec2:ModifyNetworkInterfaceAttribute", <br />"ec2:AuthorizeSecurityGroupEgress", <br />"ec2:AuthorizeSecurityGroupIngress", <br />"ec2:CreateSecurityGroup",<br />"ec2:DescribeSecurityGroups", <br />"ec2:RevokeSecurityGroupEgress",<br />"ec2:DescribeTags",<br />"ec2:DescribeInstances",<br />"ec2:DescribeSubnets",<br />"ec2:DescribeVpcs",<br />"elasticmapreduce:ListInstances", <br />"elasticmapreduce:DescribeCluster", <br />"elasticmapreduce:ListSteps"</pre>  | 
| Apply AWS tags to the network interface that EMR Studio creates when it sets up the secure network channel\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\. |  <pre>{<br />  "Effect": "Allow",<br />  "Action": "ec2:CreateTags",<br />  "Resource": "arn:aws:ec2:*:*:network-interface/*",<br />  "Condition": {<br />    "ForAllValues:StringEquals": {<br />      "aws:TagKeys": [<br />        "aws:elasticmapreduce:editor-id",<br />        "aws:elasticmapreduce:job-flow-id"<br />      ]<br />    }<br />  }<br />}</pre>  | 
| Access or upload notebook files and metadata to an Amazon S3 bucket\. |  <pre>"s3:GetObject",<br />"s3:PutObject",<br />"s3:GetEncryptionConfiguration",<br />"s3:ListBucket",<br />"s3:DeleteObject"</pre> The following permissions are only required if you use encrypted Amazon S3 buckets\. <pre>"kms:GenerateDataKey",<br />"kms:Decrypt"</pre>  | 
| Use Git credentials stored in AWS Secrets Manager to link Git repositories to a Workspace\. | <pre>"secretsmanager:GetSecretValue"</pre> | 