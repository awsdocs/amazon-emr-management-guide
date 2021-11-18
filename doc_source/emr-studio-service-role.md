# Create an EMR Studio service role<a name="emr-studio-service-role"></a>

## About the EMR Studio service role<a name="emr-studio-about-service-role"></a>

Each Amazon EMR Studio uses an IAM role with permissions that let the Studio interact with other AWS services\. This service role must include permissions that allow EMR Studio to establish a secure network channel between Workspaces and clusters, to store notebook files in Amazon S3 Control, and to access the AWS Secrets Manager while linking a Workspace to a Git repository\.

Use the Studio service role \(instead of session policies\) to define all Amazon S3 access permissions for storing notebook files, and to define AWS Secrets Manager access permissions\.

**Important**  
The example service role policy below uses tag\-based access control to identify and access the following Studio resources\. When you use the example policy, you must tag these resources with the key `"for-use-with-amazon-emr-managed-policies"` and value `"true"`\.  
The Amazon Virtual Private Cloud associated with the Studio\.
Each subnet that you associate with the Studio\.
Your EMR Studio security groups\. You must tag custom security groups\. EMR Studio tags the default security groups at creation time\. You must tag any security groups that you created during the EMR Studio preview period if you want to continue to use them\. 
Secrets maintained in AWS Secrets Manager that Studio users use to link Git repositories to a Workspace\. Tag secrets created outside of EMR Studio, or during the preview period, manually\. 
You can apply tags to resources using the **Tags** tab on the relevant resource screen in the AWS Management Console\. You can also use the AWS Resource Groups Tag Editor to apply the required tag to each EMR Studio resource at the same time\. For more information, see [Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *AWS Resource Groups* *User Guide\.*

## Prerequisites<a name="emr-studio-service-role-prereqs"></a>

To create an Amazon EMR Studio service role, you need the following items:
+ A designated AWS account for the EMR Studio\. Create the EMR Studio service role in the same account as the Studio\.
+ An IAM entity in your designated AWS account that has the necessary permissions to create a service role\. For more information, see [Service role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#id_roles_create_service-permissions) in the *AWS Identity and Access Management User Guide*\.
+ An Amazon S3 bucket where EMR Studio can back up the Workspaces and notebook files in the Studio\. Your service role must include read and write access to this bucket\.

## Instructions<a name="emr-studio-service-role-instructions"></a>

1. Follow the instructions in [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) to create the service role using the following trust policy\.

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

1. Remove any default permissions associated with the role, and include the permissions from the following sample IAM permissions policy\. Alternatively, you can create a custom policy that includes the [EMR Studio service role permissions](#emr-studio-service-role-permissions-table)\.
**Note**  
Where applicable, change `"Resource":"*"` in the following policy to specify the Amazon Resource Name \(ARN\) of the resource or resources that the statement covers for your use cases\. 
Access for the `ModifyNetworkInterfaceAttribute` API must remain as\-is in the following policy due to technical limitations with Amazon EC2 tag\-based access control and the way EMR Studio uses `ModifyNetworkInterfaceAttribute`\.
The following statements must remain unchanged in order for EMR Studio to work with the service role: `AllowAddingEMRTagsDuringDefaultSecurityGroupCreation` and `AllowAddingTagsDuringEC2ENICreation`\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "AllowEMRReadOnlyActions",
               "Effect": "Allow",
               "Action": [
                   "elasticmapreduce:ListInstances",
                   "elasticmapreduce:DescribeCluster",
                   "elasticmapreduce:ListSteps"
               ],
               "Resource": "*"
           },
           {
               "Sid": "AllowEC2ENIActionsWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateNetworkInterfacePermission",
                   "ec2:DeleteNetworkInterface"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:network-interface/*"
               ],
               "Condition": {
                   "StringEquals": {
                       "aws:ResourceTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowEC2ENIAttributeAction",
               "Effect": "Allow",
               "Action": [
                   "ec2:ModifyNetworkInterfaceAttribute"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:instance/*",
                   "arn:aws:ec2:*:*:network-interface/*",
                   "arn:aws:ec2:*:*:security-group/*"
               ]
           },
           {
               "Sid": "AllowEC2SecurityGroupActionsWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:AuthorizeSecurityGroupEgress",
                   "ec2:AuthorizeSecurityGroupIngress",
                   "ec2:RevokeSecurityGroupEgress",
                   "ec2:RevokeSecurityGroupIngress",
                   "ec2:DeleteNetworkInterfacePermission"
               ],
               "Resource": "*",
               "Condition": {
                   "StringEquals": {
                       "aws:ResourceTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowDefaultEC2SecurityGroupsCreationWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateSecurityGroup"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:security-group/*"
               ],
               "Condition": {
                   "StringEquals": {
                       "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowDefaultEC2SecurityGroupsCreationInVPCWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateSecurityGroup"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:vpc/*"
               ],
               "Condition": {
                   "StringEquals": {
                       "aws:ResourceTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowAddingEMRTagsDuringDefaultSecurityGroupCreation",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateTags"
               ],
               "Resource": "arn:aws:ec2:*:*:security-group/*",
               "Condition": {
                   "StringEquals": {
                       "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true",
                       "ec2:CreateAction": "CreateSecurityGroup"
                   }
               }
           },
           {
               "Sid": "AllowEC2ENICreationWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateNetworkInterface"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:network-interface/*"
               ],
               "Condition": {
                   "StringEquals": {
                       "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowEC2ENICreationInSubnetAndSecurityGroupWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateNetworkInterface"
               ],
               "Resource": [
                   "arn:aws:ec2:*:*:subnet/*",
                   "arn:aws:ec2:*:*:security-group/*"
               ],
               "Condition": {
                   "StringEquals": {
                       "aws:ResourceTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           },
           {
               "Sid": "AllowAddingTagsDuringEC2ENICreation",
               "Effect": "Allow",
               "Action": [
                   "ec2:CreateTags"
               ],
               "Resource": "arn:aws:ec2:*:*:network-interface/*",
               "Condition": {
                   "StringEquals": {
                       "ec2:CreateAction": "CreateNetworkInterface"
                   }
               }
           },
           {
               "Sid": "AllowEC2ReadOnlyActions",
               "Effect": "Allow",
               "Action": [
                   "ec2:DescribeSecurityGroups",
                   "ec2:DescribeNetworkInterfaces",
                   "ec2:DescribeTags",
                   "ec2:DescribeInstances",
                   "ec2:DescribeSubnets",
                   "ec2:DescribeVpcs"
               ],
               "Resource": "*"
           },
           {
               "Sid": "AllowSecretsManagerReadOnlyActionsWithEMRTags",
               "Effect": "Allow",
               "Action": [
                   "secretsmanager:GetSecretValue"
               ],
               "Resource": "arn:aws:secretsmanager:*:*:secret:*",
               "Condition": {
                   "StringEquals": {
                       "aws:ResourceTag/for-use-with-amazon-emr-managed-policies": "true"
                   }
               }
           }
       ]
   }
   ```

1. Give your service role read and write access to the Amazon S3 directory where you want to back up Workspaces and notebook files\. Use the following minimum set of permissions\. For more information, see the [Amazon S3: Allows read and write access to objects in an S3 Bucket, programmatically and in the console](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_s3_rw-bucket-console.html) example\.

   ```
   "s3:PutObject",
   "s3:GetObject",
   "s3:GetEncryptionConfiguration",
   "s3:ListBucket",
   "s3:DeleteObject"
   ```

   If you encrypt your Amazon S3 bucket, include the following permissions for AWS Key Management Service\.

   ```
   "kms:Decrypt",
   "kms:GenerateDataKey",
   "kms:ReEncrypt",
   "kms:DescribeKey"
   ```

## EMR Studio service role permissions<a name="emr-studio-service-role-permissions-table"></a>

The following table lists the operations that EMR Studio performs using the service role, along with the IAM actions required for each operation\.


****  

| Operation | Actions | 
| --- | --- | 
| Establish a secure network channel between a Workspace and an EMR cluster, and perform necessary cleanup actions\. |  <pre>"ec2:CreateNetworkInterface", <br />"ec2:CreateNetworkInterfacePermission", <br />"ec2:DeleteNetworkInterface", <br />"ec2:DeleteNetworkInterfacePermission", <br />"ec2:DescribeNetworkInterfaces", <br />"ec2:ModifyNetworkInterfaceAttribute", <br />"ec2:AuthorizeSecurityGroupEgress", <br />"ec2:AuthorizeSecurityGroupIngress", <br />"ec2:CreateSecurityGroup",<br />"ec2:DescribeSecurityGroups", <br />"ec2:RevokeSecurityGroupEgress",<br />"ec2:DescribeTags",<br />"ec2:DescribeInstances",<br />"ec2:DescribeSubnets",<br />"ec2:DescribeVpcs",<br />"elasticmapreduce:ListInstances", <br />"elasticmapreduce:DescribeCluster", <br />"elasticmapreduce:ListSteps"</pre>  | 
| Use Git credentials stored in AWS Secrets Manager to link Git repositories to a Workspace\. |  <pre>"secretsmanager:GetSecretValue"</pre>  | 
| Apply AWS tags to the network interface and default security groups that EMR Studio creates while setting up the secure network channel\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\. |  <pre>"ec2:CreateTags"</pre>  | 
| Access or upload notebook files and metadata to Amazon S3\. |  <pre>"s3:PutObject",<br />"s3:GetObject",<br />"s3:GetEncryptionConfiguration",<br />"s3:ListBucket",<br />"s3:DeleteObject" </pre> If you use an encrypted Amazon S3 bucket, include the following permissions\. <pre>"kms:Decrypt",<br />"kms:GenerateDataKey",<br />"kms:ReEncrypt",<br />"kms:DescribeKey"</pre>  | 