# How Amazon EMR Works with IAM<a name="security_iam_emr-with-iam"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources as well as the conditions under which actions are allowed or denied\. Amazon EMR supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

Amazon EMR does not support resource\-based policies\.

## Actions<a name="security_iam_emr-with-iam-id-based-policies-actions"></a>

The `Action` element of an IAM identity\-based policy describes the specific action or actions that will be allowed or denied by the policy\. Policy actions usually have the same name as the associated AWS API operation\. The action is used in a policy to grant permissions to perform the associated operation\. 

Policy actions in Amazon EMR use the following prefix before the action: `elasticmapreduce:`\. For example, to grant someone permission to create a cluster using the `RunJobFlow` API operation, you include the `elasticmapreduce:RunJobFlow` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\. Amazon EMR defines its own set of actions that describe tasks that you can perform with this service\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": [
      "elasticmapreduce:action1",
      "elasticmapreduce:action2"
```

You can specify multiple actions using wildcards \(\*\)\. For example, to specify all actions that begin with the word `Describe`, include the following action:

```
"Action": "elasticmapreduce:Describe*"
```

To see a list of Amazon EMR actions, see [Actions Defined by Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html#amazonelasticmapreduce-actions-as-permissions) in the *IAM User Guide*\.

## Resources<a name="security_iam_emr-with-iam-id-based-policies-resources"></a>

The `Resource` element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. You specify a resource using an ARN or using the wildcard \(\*\) to indicate that the statement applies to all resources\.

To see a list of Amazon EMR resource types and their ARNs, see [Resources Defined by Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html#amazonelasticmapreduce-resources-for-iam-policies) in the *IAM User Guide*\. To learn with which actions you can specify the ARN of each resource, see [Actions Defined by Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html#amazonelasticmapreduce-actions-as-permissions)\.

## Condition Keys<a name="security_iam_emr-with-iam-id-based-policies-conditionkeys"></a>

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can build conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM Policy Elements: Variables and Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

Amazon EMR defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.



 All Amazon EC2 actions support the `aws:RequestedRegion` and `ec2:Region` condition keys\. For more information, see [Example: Restricting Access to a Specific Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html#iam-example-region)\. 

To see a list of Amazon EMR condition keys, see [Condition Keys for Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html#amazonelasticmapreduce-policy-keys) in the *IAM User Guide*\. To learn with which actions and resources you can use a condition key, see [Actions Defined by Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html#amazonelasticmapreduce-actions-as-permissions)\.

### Use Cluster and Notebook Tags with IAM Policies for Access Control<a name="emr-tag-based-access"></a>

Permission for Amazon EMR actions associated with EMR Notebooks and EMR clusters can be fine\-tuned using tag\-based access control with identity\-based IAM policies\. You can use *condition keys* within a `Condition` element \(also called a `Condition` block\) to allow certain actions only when a notebook, cluster, or both has a certain tag key or key\-value combination\. You can also limit the `CreateEditor` action \(which creates an EMR notebook\) and the `RunJobFlow` action \(which creates a cluster\) so that a request for a tag must be submitted when the resource is created\.

In Amazon EMR, the condition keys that can be used in a `Condition` element apply only to those Amazon EMR API actions where `ClusterID` or `NotebookID` is a required request parameter\. For example, the [ModifyInstanceGroups](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_ModifyInstanceGroups.html) action does not support context keys because `ClusterID` is an optional parameter\.

When you create an EMR notebook, a default tag is applied with a key string of `creatorUserId` set to the value of the IAM User ID who created the notebook\. This is useful for limiting allowed actions for the notebook only to the creator\.

The following condition keys are available in Amazon EMR:
+ Use the `elasticmapreduce:ResourceTag/TagKeyString` condition context key to allow or deny user actions on clusters or notebooks with tags that have the `TagKeyString` that you specify\. If an action passes both `ClusterID` and `NotebookID`, the condition applies to both the cluster and the notebook\. This means that both resources must have the tag key string or key\-value combination that you specify\. You can use the `Resource` element to limit the statement so that it applies only to clusters or notebooks as required\. For more information, see [Amazon EMR Identity\-Based Policy Examples](security_iam_id-based-policy-examples.md)\.
+ Use the `elasticmapreduce:RequestTag/TagKeyString` condition context key to require a specific tag with actions/API calls\. For example, you can use this condition context key along with the `CreateEditor` action to require that a key with `TagKeyString` is applied to a notebook when it is created\.

## Examples<a name="security_iam_emr-with-iam-id-based-policies-examples"></a>

To view examples of Amazon EMR identity\-based policies, see [Amazon EMR Identity\-Based Policy Examples](security_iam_id-based-policy-examples.md)\.