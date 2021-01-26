# Add Required Permissions to Create and Manage an EMR Studio<a name="emr-studio-admin-permissions"></a>

## About the Required EMR Studio Permissions<a name="emr-studio-about-admin-permissions"></a>

Before you can create an Amazon EMR Studio, you must create an IAM policy that defines EMR Studio administrative permissions and add it to an identity \(user, group, or role\) in the AWS account that you designate for your Studio\. Doing so lets you assume an IAM principal that can take actions such as creating and managing a Studio, and assigning users and groups\. For detailed information about each required permission, see [Permissions Required to Manage an EMR Studio](#emr-studio-admin-permissions-table)\.

## Prerequisites<a name="emr-studio-admin-permissions-prereqs"></a>

To add the required administrative permissions for EMR Studio, you need the following items:
+ A designated AWS account for your EMR Studio\. If you use multiple accounts in your AWS organization, use a *member* account\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ An IAM identity \(user, role, or group\) in your designated AWS account to which you want to grant EMR Studio administrative permissions\.

## Instructions<a name="emr-studio-admin-permissions-instructions"></a>

1. Follow the instructions in [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) to create a policy using the following example\. Insert your own values for these items:
   + Replace *<region>* with the code of the AWS Region where you plan to create your Studio\.
   + Replace *<aws\_account\_id>* with the ID of the AWS account in which you plan to create the Studio\.
   + Replace *<EMRStudio\_Service\_Role>* and *<EMRStudio\_User\_Role>* with the names of your [EMR Studio service role](emr-studio-service-role.md) and [EMR Studio user role](emr-studio-user-role.md)\.
**Note**  
Change `"Resource":"*"` in the policy to specify the Amazon Resource Name \(ARN\) of the object or objects that the statement covers for your use cases\. `"Resource":"*"` is used here for example purposes only\.  
AWS SSO and AWS SSO Directory APIs do not support specifying an ARN in the resource element of an IAM policy statement\. To allow access to AWS SSO and AWS SSO Directory, the following permissions specify all resources, "Resource":"\*", for AWS SSO actions\. For more information, see [Actions, resources, and condition keys for AWS SSO Directory](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsssodirectory.html#awsssodirectory-actions-as-permissions)\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Resource": "arn:aws:elasticmapreduce:<region>:<aws_account_id>:studio/*",
               "Action": [
                   "elasticmapreduce:CreateStudio",
                   "elasticmapreduce:DescribeStudio",
                   "elasticmapreduce:DeleteStudio",
                   "elasticmapreduce:CreateStudioSessionMapping",
                   "elasticmapreduce:GetStudioSessionMapping",
                   "elasticmapreduce:UpdateStudioSessionMapping",
                   "elasticmapreduce:DeleteStudioSessionMapping"
               ]
           },
           {
               "Effect": "Allow",
               "Resource": "*",
               "Action": [
                   "elasticmapreduce:ListStudios",
                   "elasticmapreduce:ListStudioSessionMappings"
               ]
           },
           {
               "Effect": "Allow",
               "Resource": [
                   "arn:aws:iam::<aws_account_id>:role/<EMRStudio_Service_Role>",
                   "arn:aws:iam::<aws_account_id>:role/<EMRStudio_User_Role>"
               ],
               "Action": "iam:PassRole"
           },
           {
               "Effect": "Allow",
               "Resource": "*",
               "Action": [
                   "sso:CreateManagedApplicationInstance",
                   "sso:GetManagedApplicationInstance",
                   "sso:DeleteManagedApplicationInstance",
                   "sso:AssociateProfile",
                   "sso:DisassociateProfile",
                   "sso:ListProfiles",
                   "sso-directory:SearchUsers",
                   "sso-directory:SearchGroups",
                   "sso-directory:DescribeUser",
                   "sso-directory:DescribeGroup"
               ]
           }
       ]
   }
   ```

1. Attach the policy to the IAM identity \(user, role, or group\) to which you want to grant EMR Studio administrative permissions\. For instructions, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)\.

## Permissions Required to Manage an EMR Studio<a name="emr-studio-admin-permissions-table"></a>

This table lists the actions related to creating and managing an EMR Studio, along with the permissions needed for each action\.


****  

| Action | Permissions | 
| --- | --- | 
| Create a Studio |  <pre>"elasticmapreduce:CreateStudio", <br />"sso:CreateManagedApplicationInstance",<br />"iam:PassRole"</pre>  | 
| Describe a Studio |  <pre>"elasticmapreduce:DescribeStudio",<br />"sso:GetManagedApplicationInstance"</pre>  | 
| Delete a Studio |  <pre>"elasticmapreduce:DeleteStudio",<br />"sso:DeleteManagedApplicationInstance"</pre>  | 
| Assign users or groups to a Studio |  <pre>"elasticmapreduce:CreateStudioSessionMapping",<br />"sso:GetManagedApplicationInstance",<br />"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup"<br />"sso:ListProfiles",<br />"sso:AssociateProfile"</pre>  | 
| Update the session policy attached to a user or group assigned to a Studio |  <pre>"elasticmapreduce:UpdateStudioSessionMapping"</pre>  | 
| Remove a user or group from a Studio |  <pre>"elasticmapreduce:DeleteStudioSessionMapping",<br />"sso:GetManagedApplicationInstance",<br />"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup",<br />"sso:ListProfiles",<br />"sso:DisassociateProfile"</pre>  | 