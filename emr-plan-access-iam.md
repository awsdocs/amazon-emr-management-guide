# Use IAM Policies to Allow and Deny User Permissions<a name="emr-plan-access-iam"></a>

Amazon EMR supports AWS Identity and Access Management \(IAM\) policies\. IAM is a web service that enables AWS customers to manage users and their permissions\. You can use IAM to create policies and attach them to principals, such as users and groups\. The policies grant or deny permissions and determine what actions a user can perform with Amazon EMR and other AWS resources\. For example, you can allow a user to view EMR clusters in an AWS account but not create or delete them\. In addition, you can tag EMR clusters and then use the tags to apply fine\-grained permissions to users on individual clusters or a group of clusters that share the same tag\.

IAM is available at no charge to all AWS account holders\. You don't need to sign up for IAM\. You can use IAM through the Amazon EMR console and the AWS CLI\. You can also use it programmatically through the Amazon EMR API and the AWS SDKs\.

IAM policies adhere to the principle of least privilege, which means that a user can't perform an action until permission is granted to do so\. For more information, see the *[IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)*\.

**Topics**
+ [Amazon EMR Actions in User\-Based IAM Policies](#emr-set-iam-policy)
+ [Use Managed Policies for User Access](#emr-managed-iam-policies)
+ [Use Inline Policies for User Permissions](#emr-inline-policy)
+ [Use Cluster Tagging with IAM Policies for Cluster\-Specific Control](#emr-fine-grained-cluster-access)

## Amazon EMR Actions in User\-Based IAM Policies<a name="emr-set-iam-policy"></a>

In IAM user policies for Amazon EMR, all Amazon EMR actions are prefixed with the lowercase `elasticmapreduce` element\. You can specify the `"elasticmapreduce:*"` key, using the wildcard character \(\*\) to specify all actions related to Amazon EMR, or you can allow a subset of actions, for example, `"elasticmapreduce:Describe*"`\. You can also explicitly specify individual Amazon EMR actions, for example `"elasticmapreduce:DescribeCluster"`\. For a complete list of Amazon EMR actions, see the API action names in the [Amazon EMR API Reference](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/)\. Because Amazon EMR relies on other services such as Amazon EC2 and Amazon S3, users need to be allowed a subset of permissions for these services as well\. For more information, see [IAM Managed Policy for Full Access](#emr-managed-policy-fullaccess)\.

**Note**  
At a minimum, to access the Amazon EMR console, an IAM user needs to have an attached IAM policy that allows the following action:   

```
elasticmapreduce:ListClusters
```

For more information about permissions and policies, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

Amazon EMR does not support resource\-based and resource\-level policies, but you can use the `Condition` element \(also called the `Condition` block\) to specify fine\-grained access control based on cluster tags\. For more information, see [Use Cluster Tagging with IAM Policies for Cluster\-Specific Control](#emr-fine-grained-cluster-access)\. Because Amazon EMR does not support resource\-based or resource\-level policies, the `Resource` element always has a wildcard value\.

## Use Managed Policies for User Access<a name="emr-managed-iam-policies"></a>

The easiest way to grant full access or read\-only access to required Amazon EMR actions is to use the IAM managed policies for Amazon EMR\. Managed policies offer the benefit of updating automatically if permission requirements change\. If you use inline policies, service changes may occur that cause permission errors to appear\. 

These policies not only include actions for Amazon EMR; they also include actions for Amazon EC2, Amazon S3, and Amazon CloudWatch, which Amazon EMR uses to perform actions like launching instances, writing log files, and managing Hadoop jobs and tasks\. To create custom policies, we recommend that you begin with the managed policies and edit them according to your requirements\.

For information about how to attach policies to IAM users \(principals\), see [Working with Managed Policies Using the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#policies_using-managed-console)in the *IAM User Guide*\.

### IAM Managed Policy for Full Access<a name="emr-managed-policy-fullaccess"></a>

To grant all the required actions for Amazon EMR, attach the `AmazonElasticMapReduceFullAccess` managed policy\. The content of this policy statement is shown below\. It reveals all the actions that Amazon EMR requires for other services\. 

The contents of Version 6 of this policy are shown below\. Because the **AmazonElasticMapReduceFullAccess** policy is automatically updated, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

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

### IAM Managed Policy for Read\-Only Access<a name="emr-managed-policy-readonly"></a>

To grant read\-only privileges to Amazon EMR, attach the **AmazonElasticMapReduceReadOnlyAccess** managed policy\. The content of this policy statement is shown below\. Wildcard characters for the `elasticmapreduce` element specify that only actions that begin with the specified strings are allowed\. Keep in mind that because this policy does not explicitly deny actions, a different policy statement may still be used to grant access to specified actions\.

**Note**  
Because the **AmazonElasticMapReduceReadOnlyAccess** policy is automatically updated, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "elasticmapreduce:Describe*",
 8.         "elasticmapreduce:List*",
 9.         "elasticmapreduce:ViewEventsFromAllClustersInConsole",
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

## Use Inline Policies for User Permissions<a name="emr-inline-policy"></a>

To customize policies, we recommend that you start with a managed policy and then modify permissions and conditions according to your requirements\.

**Important**  
Inline policies are not automatically updated when service requirements change\. If you create and attach inline policies, be aware that service updates might occur that suddenly cause permissions errors\. For more information, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/policies_managed-vs-inline.html) in the *IAM User Guide* and [Specify Custom IAM Roles When You Create a Cluster](emr-iam-roles-custom.md#emr-iam-roles-launch-jobflow)\.

The `AmazonElasticMapReduceFullAccess`, which is the default managed policy for users to have full permissions for Amazon EMR, includes a statement that allows the `iam:PassRole` permissions for all resources\. This statement allows the user to pass any role to other AWS services so that Amazon EMR can interact with those services on behalf of the user\.

To implement a more restrictive policy, attach an inline policy to appropriate users or groups that allows `iam:PassRole` only for roles specific to Amazon EMR\. The following example demonstrates a statement that allows `iam:PassRole` permissions only for the default Amazon EMR roles: `EMR_DefaultRole`, `EMR_EC2_DefaultRole`, and `EMR_AutoScalingDefaultRole`\. If you use custom roles, replace the default role names with your custom role names\.

```
{
    "Action": "iam:PassRole",
    "Effect": "Allow",
    "Resource": [
        "arn:aws:iam::*:role/EMR_DefaultRole",
        "arn:aws:iam::*:role/EMR_EC2_DefaultRole",
        "arn:aws:iam::*:role/EMR_AutoScaling_DefaultRole"
    ]
}
```

For more information about roles for Amazon EMR, see [Configure IAM Roles for Amazon EMR Permissions to AWS Services](emr-iam-roles.md)\.

## Use Cluster Tagging with IAM Policies for Cluster\-Specific Control<a name="emr-fine-grained-cluster-access"></a>

You can use the `Condition` element \(also called a `Condition` block\) along with the following Amazon EMR condition context keys in an IAM user policy to control access based on cluster tags:
+ Use the `elasticmapreduce:ResourceTag/TagKeyString` condition context key to allow or deny user actions on clusters with specific tags\.
+ Use the `elasticmapreduce:RequestTag/TagKeyString` condition context key to require a specific tag with actions/API calls\. 

**Important**  
The condition context keys apply only to those Amazon EMR API actions where `ClusterID` is a required request parameter\. For example, the [ModifyInstanceGroups](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_ModifyInstanceGroups.html) action does not support context keys because `ClusterID` is an optional parameter\.

For a complete list of Amazon EMR actions, see the API action names in the [Amazon EMR API Reference](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/)\. For more information about the `Condition` element and condition operators, see [IAM Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*, particularly [String Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_String)\. For more information about adding tags to EMR clusters, see [Tagging EMR clusters](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-tags.html)\. 

### Example Amazon EMR Policy Statements<a name="emr-cluster-access-resourcetag"></a>

The following examples demonstrate different scenarios and ways to use condition operators with Amazon EMR condition context keys\. These IAM policy statements are intended for demonstration purposes only and should not be used in production environments\. There are multiple ways to combine policy statements to grant and deny permissions according to your requirements\. For more information about planning and testing IAM policies, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

#### Allow Actions Only on Clusters with Specific Tag Values<a name="emr-cluster-access-example-tagvalue"></a>

The examples below demonstrate a policy that allows a user to perform actions based on the cluster tag `department` with the value dev `dev` and also allows a user to tag clusters with that same tag\. The final policy example demonstrates how to deny privileges to tag EMR clusters with anything but that same tag\.

**Important**  
Explicitly denying permission for tagging actions is an important consideration\. This prevents users from granting permissions to themselves through cluster tags that you did not intend to grant\. If the actions shown in the last example had not been denied, a user could add and remove tags of their choosing to any cluster, and circumvent the intention of the preceding policies\.

In the following policy example, the `StringEquals` condition operator tries to match `dev` with the value for the tag `department`\. If the tag `department` hasn't been added to the cluster, or doesn't contain the value `dev`, the policy doesn't apply, and the actions aren't allowed by this policy\. If no other policy statements allow the actions, the user can only work with clusters that have this tag with this value\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt12345678901234",
      "Effect": "Allow",
      "Action": [
        "elasticmapreduce:DescribeCluster",
        "elasticmapreduce:ListSteps",
        "elasticmapreduce:TerminateJobFlows",
        "elasticmapreduce:SetTerminationProtection",
        "elasticmapreduce:ListInstances",
        "elasticmapreduce:ListInstanceGroups",
        "elasticmapreduce:ListBootstrapActions",
        "elasticmapreduce:DescribeStep"
      ],
      "Resource": [
        "*"
      ],
      "Condition": {
        "StringEquals": {
          "elasticmapreduce:ResourceTag/department": "dev"
        }
      }
    }
  ]
}
```

You can also specify multiple tag values using a condition operator\. For example, to allow all actions on clusters where the `department` tag contains the value `dev` or `test`, you could replace the condition block in the earlier example with the following\. 

```
            "Condition": {
              "StringEquals": {
                "elasticmapreduce:ResourceTag/department":["dev", "test"]
              }
            }
```

As in the preceding example, the following example policy looks for the same matching tag: the value `dev` for the `department` tag\. In this case, however, the `RequestTag` condition context key specifies that the policy applies during tag creation, so the user must create a tag that matches the specified value\.

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Sid": "Stmt1479334524000",
 6.       "Effect": "Allow",
 7.       "Action": [
 8.         "elasticmapreduce:RunJobFlow",
 9.         "iam:PassRole"
10.       ],
11.       "Resource": [
12.         "*"
13.       ],
14.       "Condition": {
15.         "StringEquals": {
16.           "elasticmapreduce:RequestTag/department": "dev"
17.         }
18.       }
19.     }
20.   ]
21. }
```

In the following example, the EMR actions that allow the addition and removal of tags is combined with a `StringNotEquals` operator specifying the `dev` tag we've seen in earlier examples\. The effect of this policy is to deny a user the permission to add or remove any tags on EMR clusters that are tagged with a `department` tag that contains the `dev` value\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
    "Effect": "Deny",
    "Action": [
      "elasticmapreduce:AddTags",
      "elasticmapreduce:RemoveTags"
      ],
      "Condition": {
        "StringNotEquals": {
          "elasticmapreduce:ResourceTag/department": "dev"
        }
      },
      "Resource": [
        "*"
      ]
    }
  ]
}
```

#### Allow Actions on Clusters with a Specific Tag, Regardless of Tag Value<a name="emr-cluster-access-example-tag"></a>

You can also allow actions only on clusters that have a particular tag, regardless of the tag value\. To do this, you can use the `Null` operator\. For more information, see [Condition Operator to Check Existence of Condition Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_Null) in the *IAM User Guide*\. For example, to allow actions only on EMR clusters that have the `department` tag, regardless of the value it contains, you could replace the Condition blocks in the earlier example with the following one\. The `Null` operator looks for the presence of the tag `department` on an EMR cluster\. If the tag exists, the `Null` statement evaluates to false, matching the condition specified in this policy statement, and the appropriate actions are allowed\. 

```
1. "Condition": {
2.   "Null": {
3.     "elasticmapreduce:ResourceTag/department":"false"
4.   }
5. }
```

The following policy statement allows a user to create an EMR cluster only if the cluster will have a `department` tag, which can contain any value\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
    "Action": [
      "elasticmapreduce:RunJobFlow",
      "iam:PassRole"
      ],
    "Condition": {
      "Null": {
        "elasticmapreduce:RequestTag/department": "false"
        }
      },
    "Effect": "Allow",
    "Resource": [
      "*"
      ]
    }
  ]
}
```

#### Require Users to Add Tags When Creating a Cluster<a name="emr-cluster-access-requesttag"></a>

The following policy statement allows a user to create an EMR cluster only if the cluster will have a `department` tag that contains the value `dev` when it is created\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:RunJobFlow",
                "iam:PassRole"
            ],
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:RequestTag/department": "dev"
                }
            },
            "Effect": "Allow",
            "Resource": [
                "*"
            ]
        }
    ]
}
```