# Amazon EMR Identity\-Based Policy Examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify Amazon EMR resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy Best Practices for Amazon EMR](security_iam_emr-with-iam-policy-best-practices.md)
+ [Allow Users to View Their Own Permissions](security_iam_id-based-policy-examples-view-own-permissions.md)
+ [Amazon EMR Managed Policies](emr-managed-iam-policies.md)
+ [IAM Policies for Tag\-Based Access to Clusters and EMR Notebooks](emr-fine-grained-cluster-access.md)