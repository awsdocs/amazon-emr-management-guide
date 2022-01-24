# IAM policies for tag\-based access to clusters and EMR notebooks<a name="emr-fine-grained-cluster-access"></a>

You can use conditions in your identity\-based policy to control access to clusters and EMR notebooks based on tags\.

For more information about adding tags to clusters, see [Tagging EMR clusters](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-tags.html)\. For more information about using condition keys, see [Condition keys](security_iam_emr-with-iam.md#security_iam_emr-with-iam-id-based-policies-conditionkeys)\.

The following examples demonstrate different scenarios and ways to use condition operators with Amazon EMR condition keys\. These IAM policy statements are intended for demonstration purposes only and should not be used in production environments\. There are multiple ways to combine policy statements to grant and deny permissions according to your requirements\. For more information about planning and testing IAM policies, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

**Important**  
Explicitly denying permission for tagging actions is an important consideration\. This prevents users from tagging a resource and thereby granting themselves permissions that you did not intend to grant\. If tagging actions for a resource are not denied, a user can modify tags and circumvent the intention of the tag\-based policies\. For an example of a policy that denies tagging actions, see [Deny access to add and remove tags](#emr-cluster-deny-tagging-example)\.

## Example identity\-based policy statements for clusters<a name="emr-cluster-access-resourcetag"></a>

The following examples demonstrate identity\-based permissions policies that are used to control the actions that are allowed with EMR clusters\.

**Important**  
The `ModifyInstanceGroup` action in Amazon EMR does not require that you specify a cluster ID\. For that reason, denying this action based on cluster tags requires additional consideration\. For more information, see [Denying the ModifyInstanceGroup action](emr-cluster-deny-modifyinstancegroup.md)\.

**Topics**
+ [Allow actions only on clusters with specific tag values](#emr-cluster-access-example-tagvalue)
+ [Require cluster tagging when a cluster is created](#emr-cluster-access-example-require-tagging)
+ [Deny access to add and remove tags](#emr-cluster-deny-tagging-example)
+ [Allow actions on clusters with a specific tag, regardless of tag value](#emr-cluster-access-example-tag)

### Allow actions only on clusters with specific tag values<a name="emr-cluster-access-example-tagvalue"></a>

The following examples demonstrate a policy that allows a user to perform actions based on the cluster tag `department` with the value `dev` and also allows a user to tag clusters with that same tag\. The final policy example demonstrates how to deny privileges to tag EMR clusters with anything but that same tag\.

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

### Require cluster tagging when a cluster is created<a name="emr-cluster-access-example-require-tagging"></a>

As in the preceding example, the following example policy looks for the same matching tag: the value `dev` for the `department` tag\. In this case, however, the `RequestTag` condition key specifies that the policy applies during tag creation, so the user must create a cluster with a tag that matches the specified value\. For the `PassRole` resource, you need to provide the AWS account ID or alias, and the service role name\. For more information about the IAM ARN format, see [IAM ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns) in the *IAM User Guide*\.

For more information specifying matching tag\-key values, see [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag) in the *IAM User Guide*\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [{
 4.             "Sid": "RunJobFlowExplicitlyWithTag",
 5.             "Effect": "Allow",
 6.             "Action": [
 7.                 "elasticmapreduce:RunJobFlow"
 8.             ],
 9.             "Resource": "*",
10.             "Condition": {
11.                 "StringEquals": {
12.                     "aws:RequestTag/department": "dev"
13.                 }
14.             }
15.         },
16.         {
17.             "Sid": "PolicyPassroleXYZ",
18.             "Effect": "Allow",
19.             "Action": [
20.                 "iam:PassRole"
21.             ],
22.             "Resource": [
23.                 "arn:aws:iam::AccountId:role/Role-Name-With-Path"
24.             ]
25.         }
26.     ]
27. }
```

### Deny access to add and remove tags<a name="emr-cluster-deny-tagging-example"></a>

In the following example, the EMR actions that allow the addition and removal of tags is combined with a `StringNotEquals` operator specifying the `dev` tag we've seen in earlier examples\. This policy prevents a user from adding or removing tags on EMR clusters with a `department` tag whose value is not `dev`\.

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

### Allow actions on clusters with a specific tag, regardless of tag value<a name="emr-cluster-access-example-tag"></a>

You can also allow actions only on clusters that have a particular tag, regardless of the tag value\. To do this, you can use the `Null` operator\. For more information, see [Condition operator to check existence of condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_Null) in the *IAM User Guide*\. For example, to allow actions only on EMR clusters that have the `department` tag, regardless of the value it contains, you could replace the Condition blocks in the earlier example with the following one\. The `Null` operator looks for the presence of the tag `department` on an EMR cluster\. If the tag exists, the `Null` statement evaluates to false, matching the condition specified in this policy statement, and the appropriate actions are allowed\. 

```
1. "Condition": {
2.   "Null": {
3.     "elasticmapreduce:ResourceTag/department":"false"
4.   }
5. }
```

The following policy statement allows a user to create an EMR cluster only if the cluster will have a `department` tag, which can contain any value\. For the `PassRole` resource, you need to provide the AWS account ID or alias, and the service role name\. For more information about the IAM ARN format, see [IAM ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns) in the *IAM User Guide*\.

For more information specifying the null \("false"\) condition operator, see [Condition operator to check existence of condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Null) in the *IAM User Guide*\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
            "Sid": "PolicyCreateClusterTagNullPassroleXYZ",
            "Effect": "Allow",
            "Action": [
                "elasticmapreduce:RunJobFlow"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "Null": {
                    "aws:RequestTag/department": "false"
                }
            }
        },
        {
            "Sid": "PolicyPassroleXYZ",
            "Effect": "Allow",
            "Action": [
                "iam:PassRole"
            ],

            "Resource": [
                "arn:aws:iam::AccountId:role/Role-Name-With-Path"
            ]
        }
    ]
}
```

## Example identity\-based policy statements for EMR Notebooks<a name="emr-managed-notebooks-tags-examples"></a>

The example IAM policy statements in this section demonstrate common scenarios for using keys to limit allowed actions using EMR Notebooks\. As long as no other policy associated with the principal \(user\) allows the actions, the condition context keys limit allowed actions as indicated\.

**Example – Allow access only to EMR Notebooks that a user creates based on tagging**  
The following example policy statement, when attached to a role or user, allows the IAM user to work only with notebooks that they have created\. This policy statement uses the default tag applied when a notebook is created\.  
In the example, the `StringEquals` condition operator tries to match a variable representing the current users IAM user ID \(`{aws:userId}`\) with the value of the tag `creatorUserID`\. If the tag `creatorUserID` hasn't been added to the notebook, or doesn't contain the value of the current user's ID, the policy doesn't apply, and the actions aren't allowed by this policy\. If no other policy statements allow the actions, the user can only work with notebooks that have this tag with this value\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:StartEditor",
                "elasticmapreduce:StopEditor",
                "elasticmapreduce:DeleteEditor",
                "elasticmapreduce:OpenEditorInConsole"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/creatorUserId": "${aws:userId}"
                }
            }
        }
    ]
}
```

**Example –Require notebook tagging when a notebook is created**  
In this example, the `RequestTag` context key is used\. The `CreateEditor` action is allowed only if the user does not change or delete the `creatorUserID` tag is added by default\. The variable $\{aws:userId\}, specifies the currently active user's User ID, which is the default value of the tag\.  
The policy statement can be used to help ensure that users do not remove the `createUserId` tag or change its value\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:CreateEditor"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:RequestTag/creatorUserId": "${aws:userid}"
                }
            }
        }
    ]
}
```
This example requires that the user create the cluster with a tag having the key string `dept` and a value set to one of the following: `datascience`, `analytics`, `operations`\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:CreateEditor"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:RequestTag/dept": [
                        "datascience",
                        "analytics",
                        "operations"
                    ]
                }
            }
        }
    ]
}
```

**Example –Limit notebook creation to tagged clusters, and require notebook tags**  
This example allows notebook creation only if the notebook is created with a tag that has the key string `owner` set to one of the specified values\. In addition, the notebook can be created only if the cluster has a tag with the key string `department` set to one of the specified values\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:CreateEditor"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:RequestTag/owner": [
                        "owner1",
                        "owner2",
                        "owner3"
                    ],
                    "elasticmapreduce:ResourceTag/department": [
                        "dep1",
                        "dep3"
                    ]
                }
            }
        }
    ]
}
```

**Example –Limit the ability to start a notebook based on tags**  
This example limits the ability to start notebooks only to those notebooks that have a tag with the key string `owner` set to one of the specified values\. Because the `Resource` element is used to specify only the `editor`, the condition does not apply to the cluster, and it does not need to be tagged\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:StartEditor"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:editor/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/owner": [
                        "owner1",
                        "owner2"
                    ]
                }
            }
        }
    ]
}
```
This example is similar to one above\. However, the limit only applies to tagged clusters, not notebooks\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:StartEditor"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:cluster/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/department": [
                        "dep1",
                        "dep3"
                    ]
                }
            }
        }
    ]
}
```
This example uses a different set of notebook and cluster tags\. It allows a notebook to be started only if:  
+ The notebook has a tag with the key string `owner` set to any of the specified values

  —and—
+ The cluster has a tag with the key string `department` set to any of the specified values

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:StartEditor"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:editor/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/owner": [
                        "user1",
                        "user2"
                    ]
                }
            }
        },
        {
            "Action": [
                "elasticmapreduce:StartEditor"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:cluster/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/department": [
                        "datascience",
                        "analytics"
                    ]
                }
            }
        }
    ]
}
```

**Example –Limit the ability to open the notebook editor based on tags**  
This example allows the notebook editor to be opened only if:  
+ The notebook has a tag with the key string `owner` set to any of the specified values\.

  —and—
+ The cluster has a tag with the key string `department` set to any of the specified values\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:OpenEditorInConsole"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:editor/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/owner": [
                        "user1",
                        "user2"
                    ]
                }
            }
        },
        {
            "Action": [
                "elasticmapreduce:OpenEditorInConsole"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticmapreduce:*:123456789012:cluster/*",
            "Condition": {
                "StringEquals": {
                    "elasticmapreduce:ResourceTag/department": [
                        "datascience",
                        "analytics"
                    ]
                }
            }
        }
    ]
}
```