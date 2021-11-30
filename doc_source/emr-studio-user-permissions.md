# Configure EMR Studio user permissions<a name="emr-studio-user-permissions"></a>

You must configure user permissions policies for Amazon EMR Studio so that you can set fine\-grained user and group permissions\. For information about how user permissions work in EMR Studio, see [Access control](how-emr-studio-works.md#emr-studio-access-control) in [How Amazon EMR Studio works](how-emr-studio-works.md)\. 

**Note**  
The permissions covered in this section don't enforce data access control\. To manage access to input datasets, you should configure permissions for the clusters that your Studio uses\. For more information, see [Security in Amazon EMR](emr-security.md)\.

## Create an EMR Studio user role for AWS SSO authentication mode<a name="emr-studio-create-user-role"></a>

You must create an EMR Studio user role when you use AWS SSO authentication mode\. 

**To create a user role for EMR Studio**

1. Follow the instructions in [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *AWS Identity and Access Management User Guide* to create a user role\.

   Use the following trust relationship policy when you create the role\.

   ```
   {
     "Version": "2008-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "elasticmapreduce.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Remove the default role permissions and policies\. 

1. Attach your EMR Studio session policies to the user role before you assign users and groups to a Studio\. For instructions on how to create session policies, see [Create permissions policies for EMR Studio users](#emr-studio-permissions-policies)\.

## Create permissions policies for EMR Studio users<a name="emr-studio-permissions-policies"></a>

Complete the following steps to create permissions policies for EMR Studio\.

**Note**  
To set Amazon S3 access permissions for storing notebook files and AWS Secrets Manager access permissions to read secrets while linking Workspaces to Git repositories, use the EMR Studio service role\. 

1. Create one or more IAM permissions policy that specify which actions a user can take in your Studio\. For example, you can create three separate policies for basic, intermediate, and advanced Studio users with the example policies on this page\. 

   The [AWS Identity and Access Management permissions for EMR Studio users](#emr-studio-iam-permissions-table) table breaks down each Studio operation that a user might perform and lists the minimum IAM actions required to perform that operation\. For instructions, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html)\.

   Your permissions policy must include the following statements\.

   ```
   {
               "Sid": "AllowAddingTagsOnSecretsWithEMRStudioPrefix",
               "Effect": "Allow",
               "Action": "secretsmanager:TagResource",
               "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*"
   },
   {
               "Sid": "AllowPassingServiceRoleForWorkspaceCreation",
               "Action": "iam:PassRole",
               "Resource": [
                   "arn:aws:iam::*:role/<your-emr-studio-service-role>"
               ],
               "Effect": "Allow"
   }
   ```

1. Attach the permissions policy to your IAM identity\. 

   The following table summarizes which IAM identity you attach a permissions policy to, depending on your EMR Studio authentication mode\. For instructions on how to attach a policy, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-user-permissions.html)

## AWS Identity and Access Management permissions for EMR Studio users<a name="emr-studio-iam-permissions-table"></a>

The following table includes each Amazon EMR Studio operation that a user might perform, and lists the minimum IAM actions needed to perform that operation\. You allow these actions in your IAM permissions policies \(when you use IAM authentication\) or in your session policies \(when you use AWS SSO authentication\) for EMR Studio\.

The table also displays the operations allowed in each of example permissions policy for EMR Studio\. For more information about the example permissions policies, see [Create permissions policies for EMR Studio users](#emr-studio-permissions-policies)\.


| Action | Basic | Intermediate | Advanced | Associated actions | 
| --- | --- | --- | --- | --- | 
| Create and delete Workspaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreateEditor", <br />"elasticmapreduce:DescribeEditor",<br />"elasticmapreduce:ListEditors", <br />"elasticmapreduce:DeleteEditor"</pre>  | 
| See a list of Amazon S3 Control storage buckets in the same account as the Studio when creating a new EMR cluster, and access container logs when using a web UI to debug applications | Yes | Yes | Yes |  <pre>"s3:ListAllMyBuckets",<br />"s3:ListBucket", <br />"s3:GetBucketLocation",<br />"s3:GetObject"</pre>  | 
| Access Workspaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:DescribeEditor", <br />"elasticmapreduce:ListEditors",<br />"elasticmapreduce:StartEditor", <br />"elasticmapreduce:StopEditor",<br />"elasticmapreduce:OpenEditorInConsole"</pre>  | 
| Attach or detach existing Amazon EMR clusters associated with the Workspace | Yes | Yes | Yes |  <pre>"elasticmapreduce:AttachEditor",<br />"elasticmapreduce:DetachEditor",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster",<br />"elasticmapreduce:ListInstanceGroups",<br />"elasticmapreduce:ListBootstrapActions"</pre>  | 
| Attach or detach Amazon EMR on EKS clusters  | Yes | Yes | Yes |  <pre>"elasticmapreduce:AttachEditor", <br />"elasticmapreduce:DetachEditor",<br />"emr-containers:ListVirtualClusters", <br />"emr-containers:DescribeVirtualCluster",<br />"emr-containers:ListManagedEndpoints",<br />"emr-containers:DescribeManagedEndpoint",<br />"emr-containers:CreateAccessTokenForManagedEndpoint"</pre>  | 
| Debug Amazon EMR on EC2 jobs with persistent application user interfaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreatePersistentAppUI",<br />"elasticmapreduce:DescribePersistentAppUI",<br />"elasticmapreduce:GetPersistentAppUIPresignedURL",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:ListSteps",<br />"elasticmapreduce:DescribeCluster",<br />"s3:ListBucket",<br />"s3:GetObject"</pre>  | 
| Debug Amazon EMR on EC2 jobs with on\-cluster application user interfaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:GetOnClusterAppUIPresignedURL"</pre>  | 
| Debug Amazon EMR on EKS job runs using the Spark History Server | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreatePersistentAppUI",<br />"elasticmapreduce:DescribePersistentAppUI",<br />"elasticmapreduce:GetPersistentAppUIPresignedURL",<br />"emr-containers:ListVirtualClusters",<br />"emr-containers:DescribeVirtualCluster",<br />"emr-containers:ListJobRuns",<br />"emr-containers:DescribeJobRun",<br />"s3:ListBucket",<br />"s3:GetObject"</pre>  | 
| Create and delete Git repositories | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreateRepository", <br />"elasticmapreduce:DeleteRepository",<br />"elasticmapreduce:ListRepositories",<br />"elasticmapreduce:DescribeRepository",<br />"secretsmanager:CreateSecret",<br />"secretsmanager:ListSecrets",<br />"secretsmanager:TagResource"</pre>  | 
| Link and unlink Git repositories | Yes | Yes | Yes |  <pre>"elasticmapreduce:LinkRepository",<br />"elasticmapreduce:UnlinkRepository",<br />"elasticmapreduce:ListRepositories",<br />"elasticmapreduce:DescribeRepository"</pre>  | 
| Create new clusters from predefined cluster templates | No | Yes | Yes |  <pre>"servicecatalog:SearchProducts", <br />"servicecatalog:DescribeProduct",<br />"servicecatalog:DescribeProductView",<br />"servicecatalog:DescribeProvisioningParameters",<br />"servicecatalog:ProvisionProduct",<br />"servicecatalog:UpdateProvisionedProduct",<br />"servicecatalog:ListProvisioningArtifacts", <br />"servicecatalog:DescribeRecord",<br />"servicecatalog:ListLaunchPaths",<br />"cloudformation:DescribeStackResources", <br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster"</pre>  | 
| Create new clusters by providing a cluster configuration | No | No | Yes |  <pre>"elasticmapreduce:RunJobFlow",<br />"iam:PassRole",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster"</pre>  | 
| Assign a user to a Studio when you use IAM authentication mode\. For more information, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\. | No | No | No |  <pre>"elasticmapreduce:CreateStudioPresignedUrl"</pre>  | 

## Example: Basic user policy<a name="emr-studio-basic-permissions-policy"></a>

The following basic user policy allows most EMR Studio actions, but does not let a user create new Amazon EMR clusters\. 

**Important**  
The example policy does not include the `CreateStudioPresignedUrl` permission, which you must allow for a user when you use IAM authentication mode\. For more information, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

The example policy includes `Condition` elements to enforce tag\-based access control \(TBAC\) so that you can use the policy with the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEMRBasicActions",
            "Action": [
                "elasticmapreduce:CreateEditor",
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:ListEditors",
                "elasticmapreduce:StartEditor",
                "elasticmapreduce:StopEditor",
                "elasticmapreduce:DeleteEditor",
                "elasticmapreduce:OpenEditorInConsole",
                "elasticmapreduce:AttachEditor",
                "elasticmapreduce:DetachEditor",
                "elasticmapreduce:CreateRepository",
                "elasticmapreduce:DescribeRepository",
                "elasticmapreduce:DeleteRepository",
                "elasticmapreduce:ListRepositories",
                "elasticmapreduce:LinkRepository",
                "elasticmapreduce:UnlinkRepository",
                "elasticmapreduce:DescribeCluster",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ListBootstrapActions",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:ListSteps",
                "elasticmapreduce:CreatePersistentAppUI",
                "elasticmapreduce:DescribePersistentAppUI",
                "elasticmapreduce:GetPersistentAppUIPresignedURL",
                "elasticmapreduce:GetOnClusterAppUIPresignedURL"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowEMRContainersBasicActions",
            "Action": [
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretManagerListSecrets",
            "Action": [
                "secretsmanager:ListSecrets"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretCreationWithEMRTagsAndEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:CreateSecret",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true"
                }
            }
        },
        {
            "Sid": "AllowAddingTagsOnSecretsWithEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:TagResource",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*"
        },
        {
            "Sid": "AllowPassingServiceRoleForWorkspaceCreation",
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::*:role/<your-emr-studio-service-role>"
            ],
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ListAndLocationPermissions",
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ReadOnlyAccessToLogs",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::aws-logs-<aws-account-id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow"
        }
    ]
}
```

## Example: Intermediate user policy<a name="emr-studio-intermediate-permissions-policy"></a>

The following intermediate user policy allows most EMR Studio actions, and lets a user create new Amazon EMR clusters using cluster templates\. 

**Important**  
The example policy does not include the `CreateStudioPresignedUrl` permission, which you must allow for a user when you use IAM authentication mode\. For more information, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

The example policy includes `Condition` elements to enforce tag\-based access control \(TBAC\) so that you can use the policy with the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEMRBasicActions",
            "Action": [
                "elasticmapreduce:CreateEditor",
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:ListEditors",
                "elasticmapreduce:StartEditor",
                "elasticmapreduce:StopEditor",
                "elasticmapreduce:DeleteEditor",
                "elasticmapreduce:OpenEditorInConsole",
                "elasticmapreduce:AttachEditor",
                "elasticmapreduce:DetachEditor",
                "elasticmapreduce:CreateRepository",
                "elasticmapreduce:DescribeRepository",
                "elasticmapreduce:DeleteRepository",
                "elasticmapreduce:ListRepositories",
                "elasticmapreduce:LinkRepository",
                "elasticmapreduce:UnlinkRepository",
                "elasticmapreduce:DescribeCluster",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ListBootstrapActions",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:ListSteps",
                "elasticmapreduce:CreatePersistentAppUI",
                "elasticmapreduce:DescribePersistentAppUI",
                "elasticmapreduce:GetPersistentAppUIPresignedURL",
                "elasticmapreduce:GetOnClusterAppUIPresignedURL"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowEMRContainersBasicActions",
            "Action": [
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretManagerListSecrets",
            "Action": [
                "secretsmanager:ListSecrets"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretCreationWithEMRTagsAndEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:CreateSecret",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true"
                }
            }
        },
        {
            "Sid": "AllowAddingTagsOnSecretsWithEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:TagResource",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*"
        },
        {
            "Sid": "AllowClusterTemplateRelatedIntermediateActions",
            "Action": [
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningParameters",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:ListLaunchPaths",
                "servicecatalog:DescribeRecord",
                "cloudformation:DescribeStackResources"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowPassingServiceRoleForWorkspaceCreation",
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::*:role/<your-emr-studio-service-role>"
            ],
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ListAndLocationPermissions",
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ReadOnlyAccessToLogs",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::aws-logs-<aws-account-id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow"
        }
    ]
}
```

## Example: Advanced user policy<a name="emr-studio-advanced-permissions-policy"></a>

The following intermediate user policy allows all EMR Studio actions, and lets a user create new Amazon EMR clusters using cluster templates or by providing a cluster configuration\. 

**Important**  
The example policy does not include the `CreateStudioPresignedUrl` permission, which you must allow for a user when you use IAM authentication mode\. For more information, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

The example policy includes `Condition` elements to enforce tag\-based access control \(TBAC\) so that you can use the policy with the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEMRBasicActions",
            "Action": [
                "elasticmapreduce:CreateEditor",
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:ListEditors",
                "elasticmapreduce:StartEditor",
                "elasticmapreduce:StopEditor",
                "elasticmapreduce:DeleteEditor",
                "elasticmapreduce:OpenEditorInConsole",
                "elasticmapreduce:AttachEditor",
                "elasticmapreduce:DetachEditor",
                "elasticmapreduce:CreateRepository",
                "elasticmapreduce:DescribeRepository",
                "elasticmapreduce:DeleteRepository",
                "elasticmapreduce:ListRepositories",
                "elasticmapreduce:LinkRepository",
                "elasticmapreduce:UnlinkRepository",
                "elasticmapreduce:DescribeCluster",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ListBootstrapActions",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:ListSteps",
                "elasticmapreduce:CreatePersistentAppUI",
                "elasticmapreduce:DescribePersistentAppUI",
                "elasticmapreduce:GetPersistentAppUIPresignedURL",
                "elasticmapreduce:GetOnClusterAppUIPresignedURL"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowEMRContainersBasicActions",
            "Action": [
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretManagerListSecrets",
            "Action": [
                "secretsmanager:ListSecrets"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowSecretCreationWithEMRTagsAndEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:CreateSecret",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/for-use-with-amazon-emr-managed-policies": "true"
                }
            }
        },
        {
            "Sid": "AllowAddingTagsOnSecretsWithEMRStudioPrefix",
            "Effect": "Allow",
            "Action": "secretsmanager:TagResource",
            "Resource": "arn:aws:secretsmanager:*:*:secret:emr-studio-*"
        },
        {
            "Sid": "AllowClusterTemplateRelatedIntermediateActions",
            "Action": [
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningParameters",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:ListLaunchPaths",
                "servicecatalog:DescribeRecord",
                "cloudformation:DescribeStackResources"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowEMRCreateClusterAdvancedActions",
            "Action": [
                "elasticmapreduce:RunJobFlow"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowPassingServiceRoleForWorkspaceCreation",
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::*:role/<your-emr-studio-service-role>",
                "arn:aws:iam::*:role/EMR_DefaultRole",
                "arn:aws:iam::*:role/EMR_EC2_DefaultRole"
            ],
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ListAndLocationPermissions",
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow"
        },
        {
            "Sid": "AllowS3ReadOnlyAccessToLogs",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                
                "arn:aws:s3:::aws-logs-<aws-account-id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow"
        }
    ]
}
```