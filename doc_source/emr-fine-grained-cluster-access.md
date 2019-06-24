# IAM Policies for Tag\-Based Access to Clusters and EMR Notebooks<a name="emr-fine-grained-cluster-access"></a>

You can use conditions in your identity\-based policy to control access to clusters and EMR notebooks based on tags\.

For more information about adding tags to clusters, see [Tagging EMR clusters](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-tags.html)\. For more information about using condition keys, see [Condition Keys](security_iam_emr-with-iam.md#security_iam_emr-with-iam-id-based-policies-conditionkeys)\.

The following examples demonstrate different scenarios and ways to use condition operators with Amazon EMR condition keys\. These IAM policy statements are intended for demonstration purposes only and should not be used in production environments\. There are multiple ways to combine policy statements to grant and deny permissions according to your requirements\. For more information about planning and testing IAM policies, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Example Identity\-Based Policy Statements for Clusters<a name="emr-cluster-access-resourcetag"></a>

The examples below demonstrate identity\-based permissions policies that are used to control the actions that are allowed with EMR clusters\.

### Allow Actions Only on Clusters with Specific Tag Values<a name="emr-cluster-access-example-tagvalue"></a>

The examples below demonstrate a policy that allows a user to perform actions based on the cluster tag `department` with the value `dev` and also allows a user to tag clusters with that same tag\. The final policy example demonstrates how to deny privileges to tag EMR clusters with anything but that same tag\.

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

As in the preceding example, the following example policy looks for the same matching tag: the value `dev` for the `department` tag\. In this case, however, the `RequestTag` condition key specifies that the policy applies during tag creation, so the user must create a tag that matches the specified value\.

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

### Allow Actions on Clusters with a Specific Tag, Regardless of Tag Value<a name="emr-cluster-access-example-tag"></a>

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

### Require Users to Add Tags When Creating a Cluster<a name="emr-cluster-access-requesttag"></a>

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

## Example Identity\-Based Policy Statements for EMR Notebooks<a name="emr-managed-notebooks-tags-examples"></a>

The example IAM policy statements in this section demonstrate common scenarios for using keys to limit allowed actions using EMR Notebooks\. As long as no other policy associated with the principal \(user\) allows the actions, the condition context keys limit allowed actions as indicated\.

**Example – Allow access only to notebooks that a user creates based on tagging**  
The example policy statement below, when attached to a role or user, allows the IAM user to work only with notebooks that they have created\. This policy statement uses the default tag applied when a notebook is created\.  
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