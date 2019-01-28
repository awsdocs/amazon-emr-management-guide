# Using Tags to Control User Permissions<a name="emr-managed-notebooks-tags"></a>

Permission for Amazon EMR actions associated with EMR Notebooks can be fine\-tuned using tag\-based access control in IAM policies\. You can use a `Condition` element \(also called a `Condition` block\) to allow certain actions only when a notebook, cluster, or both has a certain tag key or key\-value combination\. You can also limit the `CreateEditor` action so that a request for a tag must be submitted when a user creates a notebook\.

When you create an EMR notebook, a default tag is applied with a key string of `creatorUserId` set to the value of the IAM User ID who created the notebook\. This is useful for limiting allowed actions for the notebook only to the creator\.

The context keys that are available for Amazon EMR resources are available for notebooks as well:
+ Use the `elasticmapreduce:ResourceTag/TagKeyString` condition context key to allow or deny user actions on clusters with tags that have the `TagKeyString` that you specify\.
+ Use the `elasticmapreduce:RequestTag/TagKeyString` condition context key along with the `CreateEditor` action to require that a key with `TagKeyString` is applied to a notebook when it's created\.

For more information about using resource tags with actions that pass `ClusterID` as a required request parameter, see [Use Cluster Tagging with IAM Policies for Cluster\-Specific Control](emr-plan-access-iam.md#emr-fine-grained-cluster-access)\.

If an action passes both `ClusterID` and `NotebookID` and you use the `ResourceTag` condition context key, the condition applies to both the cluster and the notebook\. This means that both resources must have the tag key string or key\-value combination\. You can use the `Resource` element to limit the statement so that it applies only to clusters or notebooks as required\. For more information, see the examples in the following sections\.

## Example Policy Statements Using Tags as Context Keys<a name="emr-managed-notebooks-tags-examples"></a>

The example IAM policy statements in this section demonstrate common scenarios for using keys to limit allowed actions using EMR Notebooks\. As long as no other policy associated with the principal \(user\) allows the actions, the condition context keys limit allowed actions as indicated\.

**Example –Allow access only to notebooks that a user creates based on tagging**  
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