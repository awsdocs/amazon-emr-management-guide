# Allow users and groups to create and modify roles<a name="emr-iam-roles-create-permissions"></a>

IAM principals \(users and groups\) who create, modify, and specify roles for a cluster, including default roles, must be allowed to perform the following actions\. For details about each action, see [Actions](https://docs.aws.amazon.com/IAM/latest/APIReference/API_Operations.html) in the *IAM API Reference*\.
+ `iam:CreateRole`
+ `iam:PutRolePolicy`
+ `iam:CreateInstanceProfile`
+ `iam:AddRoleToInstanceProfile`
+ `iam:ListRoles`
+ `iam:GetPolicy`
+ `iam:GetInstanceProfile`
+ `iam:GetPolicyVersion`
+ `iam:AttachRolePolicy`
+ `iam:PassRole`

The `iam:PassRole` permission allows cluster creation\. The remaining permissions allow the creation of the default roles\.

For information about assigning permissions to an IAM user, see [Changing permissions for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.