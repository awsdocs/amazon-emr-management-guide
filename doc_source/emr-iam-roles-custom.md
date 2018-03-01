# Customize IAM Roles<a name="emr-iam-roles-custom"></a>

You may want to customize IAM roles and permissions for your requirements\. For example, if your application does not use EMRFS consistent view, you may not want to allow Amazon EMR to access Amazon DynamoDB\. To customize permissions, we recommend that you create new roles and policies\. Begin with the permissions in the managed policies for the default roles \(for example, `AmazonElasticMapReduceforEC2Role` and `AmazonElasticMapReduceRole`\), copy and paste the contents to new policy statements, modify the permissions as appropriate, and attach the modified permissions policies to the roles that you create\. You must have the appropriate IAM permissions to work with roles and policies\. For more information, see [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)\.

**Important**  
Inline policies are not automatically updated when service requirements change\. If you create and attach inline policies, be aware that service updates might occur that suddenly cause permission errors\. For more information, see [Managed Policies and Inline Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/policies_managed-vs-inline.html) in the *IAM User Guide* and [Specify Custom IAM Roles When You Create a Cluster](emr-iam-roles-launch-jobflow.md)\.

For more information about working with IAM roles, see the following topics in the *IAM User Guide*:

+  [Creating a Role to Delegate Permissions to an AWS Service](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) 

+  [Modifying a Role](http://docs.aws.amazon.com/IAM/latest/UserGuide/modifying-role.html) 

+  [Deleting a Role](http://docs.aws.amazon.com/IAM/latest/UserGuide/deleting-roles.html) 