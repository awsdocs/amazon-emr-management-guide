# Create an EMR Studio User Role with Session Policies<a name="emr-studio-user-role"></a>

## About the EMR Studio User Role and Session Policies<a name="emr-studio-about-user-role"></a>

When a user logs in to an EMR Studio, the Studio assumes your EMR Studio user role\. The Studio then grants specific user permissions based on an IAM session policy that you specify when you assign the user to the Studio\. 

Session policies let you set specific Studio permissions at the user or group level without the need to create multiple IAM roles\. For more information about session policies, see [Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_session) in the *AWS Identity and Access Management User Guide*\. 

You must attach your session policies to your EMR Studio user role\. Then, when you [assign users and groups](emr-studio-assign-users-groups.md) to your Studio, you map a session policy to that user or group to apply fine\-grained permission controls\. Amazon EMR stores these session policy mappings\.

We’ve provided example templates that you can use to create session policies for EMR Studio\. For more information about each example policy and its permissions, see [Session Policy Permissions](#emr-studio-session-policy-table)\. You may also create custom session policies to fit your team’s use cases\.

**Note**  
To set Amazon S3 access permissions for storing notebook files, and AWS Secrets Manager access permissions to read secrets while linking Workspaces to Git repositories, use the EMR Studio service role\. 

## Prerequisites<a name="emr-studio-user-role-prereqs"></a>

To create a user role and session policies for EMR Studio, you need the following items:
+ A designated AWS account for your EMR Studio\. You must create your EMR Studio user role and session policies in the same account\. If you use multiple accounts in your AWS organization, use a *member* account\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ An IAM principal in your designated AWS account that has the necessary permissions to create a user role with permissions policies\. For more information, see [Service role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#id_roles_create_service-permissions) in the *AWS Identity and Access Management User Guide*\.

## Step 1: Create Session Policies for Studio Users and Groups<a name="emr-studio-session-policies"></a>

Follow the instructions in [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) to define different session policies for your Studio users and groups\. You may create custom session policies to fit your team’s use cases, or use the provided example [basic](#emr-studio-basic-session-policy), [intermediate](#emr-studio-intermediate-session-policy), or [advanced](#emr-studio-advanced-session-policy) policy templates\. For more information about each example policy and its permissions, see [Session Policy Permissions](#emr-studio-session-policy-table)\. The table also breaks down each Studio action that a user might perform and lists the minimum permissions needed to perform that action\.

To modify the example templates, use the following guidelines:
+ Replace *<region>* with the code of the AWS Region associated with your Studio\.
+ Replace *<aws\_account\_id>* with the ID of the AWS account associated with your Studio\.
+ Replace *<EMRStudio\_Service\_Role>* with the name of your EMR Studio service role\.
+ Replace *<default\-s3\-storage\-bucket\-name>* with the name of the default Amazon S3 bucket for your Studio\. If you do not set a default storage location, you can remove the bucket ARN from the list of resources\.

**Note**  
Change `"Resource":"*"` in the example policies to specify the Amazon Resource Name \(ARN\) of the object or objects that the statement covers for your use cases\. `"Resource":"*"` is used here for example purposes only\.

## Step 2: Create an EMR Studio User Role<a name="emr-studio-create-user-role"></a>

Follow the instructions in [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *AWS Identity and Access Management User Guide* to create your EMR Studio user role\.

Use the following trust relationship policy when you create the user role\. You should also remove the default permissions or policies associated with the role, and attach all of your EMR Studio session policies\. 

After you create the role, you specify it by Amazon Resource Name \(ARN\) when you create a Studio\. 

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

## Session Policy Permissions<a name="emr-studio-session-policy-table"></a>

This table lists the actions that each example session policy allows, along with the permissions needed to perform each action\. This table also includes each Studio action that a user might perform, and lists the minimum permissions needed to perform that action\.


| Action | Basic | Intermediate | Advanced | Associated Permissions | 
| --- | --- | --- | --- | --- | 
| Create and delete Workspaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreateEditor", <br />"elasticmapreduce:DescribeEditor",<br />"elasticmapreduce:ListEditors", <br />"elasticmapreduce:DeleteEditor"</pre>  | 
| See a list of all Amazon S3 storage buckets in the same account as the Studio, and associate a Workspace with a bucket | Yes | Yes | Yes |  <pre>"s3:ListAllMyBuckets", <br />"s3:GetBucketLocation"</pre>  | 
| Access Workspaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:DescribeEditor", <br />"elasticmapreduce:ListEditors",<br />"elasticmapreduce:StartEditor", <br />"elasticmapreduce:StopEditor",<br />"elasticmapreduce:OpenEditorInConsole"</pre>  | 
| Attach or detach existing Amazon EMR clusters associated with the Workspace | Yes | Yes | Yes |  <pre>"elasticmapreduce:AttachEditor",<br />"elasticmapreduce:DetachEditor",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster",<br />"elasticmapreduce:ListInstanceGroups",<br />"elasticmapreduce:ListBootstrapActions"</pre>  | 
| Attach or detach Amazon EMR on EKS clusters  | Yes | Yes | Yes |  <pre>"elasticmapreduce:AttachEditor", <br />"elasticmapreduce:DetachEditor",<br />"emr-containers:ListVirtualClusters", <br />"emr-containers:DescribeVirtualCluster",<br />"emr-containers:ListManagedEndpoints",<br />"emr-containers:DescribeManagedEndpoint",<br />"emr-containers:CreateAccessTokenForManagedEndpoint"</pre>  | 
| Debug Amazon EMR on EC2 jobs with persistent application user interfaces | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreatePersistentAppUI",<br />"elasticmapreduce:DescribePersistentAppUI",<br />"elasticmapreduce:GetPersistentAppUIPresignedURL",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster",<br />"s3:ListBucket",<br />"s3:GetObject"</pre>  | 
| Debug Amazon EMR on EKS job runs using the Spark history server | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreatePersistentAppUI",<br />"elasticmapreduce:DescribePersistentAppUI",<br />"elasticmapreduce:GetPersistentAppUIPresignedURL",<br />"emr-containers:ListVirtualClusters",<br />"emr-containers:DescribeVirtualCluster",<br />"emr-containers:ListJobRuns",<br />"emr-containers:DescribeJobRun",<br />"s3:ListBucket",<br />"s3:GetObject"</pre>  | 
| Create and delete Git repositories | Yes | Yes | Yes |  <pre>"elasticmapreduce:CreateRepository", <br />"elasticmapreduce:DeleteRepository",<br />"elasticmapreduce:ListRepositories",<br />"elasticmapreduce:DescribeRepository",<br />"secretsmanager:CreateSecret",<br />"secretsmanager:ListSecrets"</pre>  | 
| Link and unlink Git repositories | Yes | Yes | Yes |  <pre>"elasticmapreduce:LinkRepository",<br />"elasticmapreduce:UnlinkRepository",<br />"elasticmapreduce:ListRepositories",<br />"elasticmapreduce:DescribeRepository"</pre>  | 
| Create new clusters from predefined cluster templates | No | Yes | Yes |  <pre>"servicecatalog:SearchProducts", <br />"servicecatalog:DescribeProduct",<br />"servicecatalog:DescribeProductView",<br />"servicecatalog:DescribeProvisioningParameters",<br />"servicecatalog:ProvisionProduct",<br />"servicecatalog:UpdateProvisionedProduct",<br />"servicecatalog:ListProvisioningArtifacts", <br />"servicecatalog:DescribeRecord",<br />"cloudformation:DescribeStackResources", <br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster"</pre>  | 
| Create new clusters by explicitly providing a cluster configuration | No | No | Yes |  <pre>"elasticmapreduce:RunJobFlow",<br />"iam:PassRole",<br />"elasticmapreduce:ListClusters",<br />"elasticmapreduce:DescribeCluster"</pre>  | 

## Example: Basic User Session Policy<a name="emr-studio-basic-session-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
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
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowBasicActions"
        },
        {
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::<aws_account_id>:role/<EMRStudio_Service_Role>"
            ],
            "Effect": "Allow",
            "Sid": "PassRolePermission"
        },
        {
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow",
            "Sid": "S3ListPermission"
        },
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<default-s3-storage-bucket-name>/*",
                "arn:aws:s3:::aws-logs-<aws_account_id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow",
            "Sid": "S3GetObjectPermission"
        }
    ]
}
```

## Example: Intermediate User Session Policy<a name="emr-studio-intermediate-session-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
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
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowBasicActions"
        },
        {
            "Action": [
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningParameters",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:DescribeRecord",
                "cloudformation:DescribeStackResources"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowIntermediateActions"
        },
        {
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::<aws_account_id>:role/<EMRStudio_Service_Role>"
            ],
            "Effect": "Allow",
            "Sid": "PassRolePermission"
        },
        {
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow",
            "Sid": "S3ListPermission"
        },
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<default-s3-storage-bucket-name>/*",
                "arn:aws:s3:::aws-logs-<aws_account_id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow",
            "Sid": "S3GetObjectPermission"
        }
    ]
}
```

## Example: Advanced User Session Policy<a name="emr-studio-advanced-session-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
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
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "emr-containers:DescribeVirtualCluster",
                "emr-containers:ListVirtualClusters",
                "emr-containers:DescribeManagedEndpoint",
                "emr-containers:ListManagedEndpoints",
                "emr-containers:CreateAccessTokenForManagedEndpoint",
                "emr-containers:DescribeJobRun",
                "emr-containers:ListJobRuns"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowBasicActions"
        },
        {
            "Action": [
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningParameters",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:DescribeRecord",
                "cloudformation:DescribeStackResources"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowIntermediateActions"
        },
        {
            "Action": [
                "elasticmapreduce:RunJobFlow"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AllowAdvancedActions"
        },
        {
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::<aws_account_id>:role/<EMRStudio_Service_Role>",
                "arn:aws:iam::<aws_account_id>:role/EMR_DefaultRole",
                "arn:aws:iam::<aws_account_id>:role/EMR_EC2_DefaultRole"
            ],
            "Effect": "Allow",
            "Sid": "PassRolePermission"
        },
        {
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::*",
            "Effect": "Allow",
            "Sid": "S3ListPermission"
        },
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<default-s3-storage-bucket-name>/*",
                "arn:aws:s3:::aws-logs-<aws_account_id>-<region>/elasticmapreduce/*"
            ],
            "Effect": "Allow",
            "Sid": "S3GetObjectPermission"
        }
    ]
}
```