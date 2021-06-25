# Amazon EMR identity\-based policy examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify Amazon EMR resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating policies on the JSON tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices for Amazon EMR](security_iam_emr-with-iam-policy-best-practices.md)
+ [Allow users to view their own permissions](security_iam_id-based-policy-examples-view-own-permissions.md)
+ [Amazon EMR managed policies](emr-managed-iam-policies.md)
+ [IAM policies for tag\-based access to clusters and EMR notebooks](emr-fine-grained-cluster-access.md)
+ [Denying the ModifyInstanceGroup action](emr-cluster-deny-modifyinstancegroup.md)