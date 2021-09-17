# Configure EMR Studio user permissions<a name="emr-studio-user-permissions"></a>

In Amazon EMR Studio, you configure user authorization \(permissions\) with AWS Identity and Access Management \(IAM\) identity\-based policies\. In these policies, you specify allowed actions and resources, as well as the conditions under which the actions are allowed\.

You can follow the same steps to create permissions policies for EMR Studio regardless of authentication mode\. However, the method you use to *apply* a policy to a user differs by authentication mode\. 
+ With IAM authentication or federation, you create one or more IAM permissions policies for EMR Studio and apply the policies directly to IAM identities \(users, groups, or roles\) for EMR Studio\. 
+ With AWS SSO authentication, you create a single EMR Studio user role with session policies\. Then, when you [assign users and groups](emr-studio-manage-users.md#emr-studio-assign-users-groups) to a Studio, you map a session policy to that user or group to apply the permissions\.

**Note**  
To set Amazon S3 access permissions for storing notebook files and AWS Secrets Manager access permissions to read secrets while linking Workspaces to Git repositories, use the EMR Studio service role\. 

## User permissions for IAM authentication mode<a name="emr-studio-iam-authorization"></a>

To set user permissions when you use IAM authentication for EMR Studio, you allow actions such as `elasticmapreduce:RunJobFlow` in an IAM permissions policy\. You can create one or more permissions policies to use\. For example, you might create a basic policy that does not allow a user to create new Amazon EMR clusters, and another policy that does allow cluster creation\. For a list of all Studio actions, see [AWS Identity and Access Management permissions for EMR Studio users](emr-studio-iam-permissions-table.md)\.

After you create your policies, you attach the policies to your IAM identities \(users, groups of users, or roles\) for EMR Studio\. 

The following table summarizes how to attach a permissions policy when you use IAM authentication\.


****  

| When you use\.\.\. | Attach the policy to\.\.\. | 
| --- | --- | 
| IAM authentication | Your IAM identities \(users, groups of users, or roles\)\. For example, you can attach a permissions policy to a user in your AWS account\. | 
| IAM federation with an external identity provider \(IdP\) |  The IAM role or roles that you create for your external IdP\. For example, an IAM role for SAML 2\.0 federation\.  EMR Studio uses the permissions that you attach to your IAM roles for users with federated access to a Studio\.  | 

For information about the difference between IAM authentication and IAM federation, see [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\.

## User permissions for AWS SSO authentication mode<a name="emr-studio-sso-authorization"></a>

When you use AWS SSO authentication, you create a single EMR Studio user role\. The *user role* is a dedicated IAM role that a Studio assumes when a user logs in\.

You attach IAM session policies to the EMR Studio user role\. A *session policy* is a special kind of IAM permissions policy that limits what a federated user can do during a Studio login session\. Session policies let you set specific permissions for a user or group without creating multiple user roles for EMR Studio\.

When you [assign users and groups](emr-studio-manage-users.md#emr-studio-assign-users-groups) to a Studio, you map a session policy to that user or group to apply fine\-grained permissions\. You can also update a user or group's session policy at any time\. Amazon EMR stores each session policy mapping that you create\.

For more information about session policies, see [Policies and permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_session) in the *AWS Identity and Access Management User Guide*\. 

## Create an EMR Studio user role for AWS SSO authentication mode<a name="emr-studio-create-user-role"></a>

When you use AWS SSO authentication mode for EMR Studio, you must create a user role\. The user role is a dedicated IAM role that your Studio assumes when a user logs in\. 

Before you create a user role for EMR Studio, you need the following:
+ An AWS account for your EMR Studio\. Create your user role and session policies in the same account\. If you use multiple accounts in your AWS organization, use a *member* account\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ The necessary permissions to create an IAM role\. For more information, see [Service role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#id_roles_create_service-permissions) in the *AWS Identity and Access Management User Guide*\.

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

1. Remove the default permissions or policies associated with the role\. 

1. Attach all of your EMR Studio session policies to the user role before you assign users and groups to a Studio\. For instructions on how to create session policies, see [Create permissions policies for EMR Studio users](#emr-studio-permissions-policies)\.

## Create permissions policies for EMR Studio users<a name="emr-studio-permissions-policies"></a>

You can use the following instructions to create permissions policies for EMR Studio regardless of the authentication mode that you use \(IAM or AWS SSO\)\. 

Before you create a policy, you must be authorized to create IAM resources\. For more information, see [Service role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#id_roles_create_service-permissions) in the *AWS Identity and Access Management User Guide*\.

For more information about the permissions in each example policy, see [AWS Identity and Access Management permissions for EMR Studio users](emr-studio-iam-permissions-table.md)\.

1. Follow the instructions in [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) to create a permissions policy\. The [AWS Identity and Access Management permissions for EMR Studio users](emr-studio-iam-permissions-table.md) table breaks down each Studio operation that a user might perform and lists the minimum IAM actions required to perform that operation\.

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

   You can repeat this step to create different permissions policies as you see fit\. For example, you might create three separate policies for basic, intermediate, and advanced Studio users\.

1. Attach the permissions policy to your IAM identity\. 

   The following table summarizes which IAM identity you attach a permissions policy to, depending on your EMR Studio authentication mode\. For instructions on how to attach a policy, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-user-permissions.html)

   For information about the differences between IAM authentication, IAM federation, and AWS SSO authentication, see [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\.

## Example: Basic user policy<a name="emr-studio-basic-permissions-policy"></a>

The following basic user policy allows most EMR Studio actions, but does not let a user create new Amazon EMR clusters\. The policy includes `Condition` elements, which enforce tag\-based access control \(TBAC\) for the `"for-use-with-amazon-emr-managed-policies": "true"` tag\. The same TBAC is defined in the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

To modify the example permissions policy, use the following guidelines:
+ Replace *<region>* with the code of the AWS Region associated with the Studio\.
+ Replace *<aws\-account\-id>* with the ID of the AWS account associated with the Studio\.
+ Replace *<your\-emr\-studio\-service\-role>* with the name of your EMR Studio service role\.

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

The following intermediate user policy allows most EMR Studio actions, and lets a user create new Amazon EMR clusters using cluster templates\. The policy includes `Condition` elements, which enforce tag\-based access control \(TBAC\) for the `"for-use-with-amazon-emr-managed-policies": "true"` tag\. The same TBAC is defined in the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

To modify the example permissions policy, use the following guidelines:
+ Replace *<region>* with the code of the AWS Region associated with the Studio\.
+ Replace *<aws\-account\-id>* with the ID of the AWS account associated with the Studio\.
+ Replace *<your\-emr\-studio\-service\-role>* with the name of your EMR Studio service role\.

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

The following intermediate user policy allows all EMR Studio actions, and lets a user create new Amazon EMR clusters using cluster templates or by providing a cluster configuration\. The policy includes `Condition` elements, which enforce tag\-based access control \(TBAC\) for the `"for-use-with-amazon-emr-managed-policies": "true"` tag\. The same TBAC is defined in the example service role for EMR Studio\. For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

To modify the example permissions policy, use the following guidelines:
+ Replace *<region>* with the code of the AWS Region associated with the Studio\.
+ Replace *<aws\-account\-id>* with the ID of the AWS account associated with the Studio\.
+ Replace *<your\-emr\-studio\-service\-role>* with the name of your EMR Studio service role\.

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