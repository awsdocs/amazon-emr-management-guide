# Permissions required to create and manage an EMR Studio<a name="emr-studio-admin-permissions"></a>

## About the required EMR Studio permissions<a name="emr-studio-about-admin-permissions"></a>

These permissions described on this page let you create and manage EMR Studios\. Before you create an Amazon EMR Studio, make sure you have these permissions\. For detailed information about each required permission, see [Permissions required to manage an EMR Studio](#emr-studio-admin-permissions-table)\.

## Prerequisites<a name="emr-studio-admin-permissions-prereqs"></a>

To add the required administrative permissions for EMR Studio, you need the following items:
+ A designated AWS account for the EMR Studio\.
+ An IAM identity \(user, role, or group\) in the designated AWS account that you want to grant EMR Studio administrative permissions to\.

## Instructions<a name="emr-studio-admin-permissions-instructions"></a>

1. Follow the instructions in [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) to create a policy using one of the following examples\. The permissions you need depend on your [authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\. 

   Insert your own values for these items:
   + Replace *`<your-resource-ARN>` *to specify the Amazon Resource Name \(ARN\) of the object or objects that the statement covers for your use cases\.
   + Replace *<region>* with the code of the AWS Region where you plan to create the Studio\.
   + Replace *<aws\-account\_id>* with the ID of the AWS account for the Studio\.
   + Replace *<EMRStudio\-Service\-Role>* and *<EMRStudio\-User\-Role>* with the names of your [EMR Studio service role](emr-studio-service-role.md) and [EMR Studio user role](emr-studio-user-permissions.md#emr-studio-create-user-role)\.  
**Example Admin permissions when you use IAM authentication mode**  

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Resource": "arn:aws:elasticmapreduce:<region>:<aws-account-id>:studio/*",
               "Action": [
                   "elasticmapreduce:CreateStudio",
                   "elasticmapreduce:DescribeStudio",
                   "elasticmapreduce:DeleteStudio"
               ]
           },
           {
               "Effect": "Allow",
               "Resource": "<your-resource-ARN>",
               "Action": [
                   "elasticmapreduce:ListStudios"
               ]
           },
           {
               "Effect": "Allow",
               "Resource": [
                   "arn:aws:iam::<aws-account-id>:role/<EMRStudio-Service-Role>"
               ],
               "Action": "iam:PassRole"
           }
       ]
   }
   ```  
**Example Admin permissions when you use AWS SSO authentication mode**  
**Note**  
AWS SSO and AWS SSO Directory APIs don't support specifying an ARN in the resource element of an IAM policy statement\. To allow access to AWS SSO and AWS SSO Directory, the following permissions specify all resources, "Resource":"\*", for AWS SSO actions\. For more information, see [Actions, resources, and condition keys for AWS SSO Directory](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsssodirectory.html#awsssodirectory-actions-as-permissions)\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Resource": "arn:aws:elasticmapreduce:<region>:<aws-account-id>:studio/*",
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
               "Resource": "<your-resource-ARN>",
               "Action": [
                   "elasticmapreduce:ListStudios",
                   "elasticmapreduce:ListStudioSessionMappings"
               ]
           },
           {
               "Effect": "Allow",
               "Resource": [
                   "arn:aws:iam::<aws-account-id>:role/<EMRStudio-Service-Role>",
                   "arn:aws:iam::<aws-account-id>:role/<EMRStudio-User-Role>"
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
                   "sso:GetProfile",
                   "sso:ListDirectoryAssociations",
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

1. Attach the policy to your designated IAM identity \(user, role, or group\)\. For instructions, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)\.

## Permissions required to manage an EMR Studio<a name="emr-studio-admin-permissions-table"></a>

The following table lists the operations related to creating and managing an EMR Studio\. The table also displays the permissions needed for each operation\.

**Note**  
You only need AWS SSO and Studio `SessionMapping` actions when you use AWS SSO authentication mode\.


****  

| Operation | Permissions | 
| --- | --- | 
| Create a Studio |  <pre>"elasticmapreduce:CreateStudio", <br />"sso:CreateManagedApplicationInstance",<br />"iam:PassRole"</pre>  | 
| Describe a Studio |  <pre>"elasticmapreduce:DescribeStudio",<br />"sso:GetManagedApplicationInstance"</pre>  | 
| List Studios |  <pre>"elasticmapreduce:ListStudios"</pre>  | 
| Delete a Studio |  <pre>"elasticmapreduce:DeleteStudio",<br />"sso:DeleteManagedApplicationInstance"</pre>  | 
| Additional permissions required when you use AWS SSO mode | 
|  Assign users or groups to a Studio  |  <pre>"elasticmapreduce:CreateStudioSessionMapping",<br />"sso:GetProfile",<br />"sso:ListDirectoryAssociations",<br />"sso:ListProfiles",<br />"sso:AssociateProfile"<br />"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup"</pre>  | 
|  Retrieve Studio assignment details for a specific user or group  |  <pre>"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup",<br />"sso:GetManagedApplicationInstance",<br />"elasticmapreduce:GetStudioSessionMapping"</pre>  | 
| List all users and groups assigned to a Studio |  <pre>"elasticmapreduce:ListStudioSessionMappings"</pre>  | 
| Update the session policy attached to a user or group assigned to a Studio |  <pre>"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup",<br />"sso:GetManagedApplicationInstance",<br />"elasticmapreduce:UpdateStudioSessionMapping"</pre>  | 
| Remove a user or group from a Studio |  <pre>"elasticmapreduce:DeleteStudioSessionMapping",<br />"sso-directory:SearchUsers",<br />"sso-directory:SearchGroups",<br />"sso-directory:DescribeUser",<br />"sso-directory:DescribeGroup",<br />"sso:ListDirectoryAssociations",<br />"sso:GetProfile",<br />"sso:GetManagedApplicationInstance"<br />"sso:ListProfiles",<br />"sso:DisassociateProfile"</pre>  | 