# Manage Users and Groups Assigned to Your Amazon EMR Studio<a name="emr-studio-manage-users"></a>

## Assign a User or Group to Your EMR Studio<a name="emr-studio-assign-users-groups"></a>

After you create an EMR Studio, you can assign users and groups using the EMR console or the AWS CLI\. You can also use the [AWS::EMR::StudioSessionMapping](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-emr-studiosessionmapping.html) resource template to assign users or groups using AWS CloudFormation\.

When you assign a user or group to an EMR Studio, you must specify a session policy that defines fine\-grained permissions, such as the ability to create a new EMR cluster, for that user or group\. Amazon EMR stores these session policy mappings\. You can also update the session policy that is mapped to a user or group after assignment\.

**Note**  
The final permissions for a user or group is an intersection of the permissions defined in your EMR Studio user role and the permissions defined in the session policy that is mapped to the user or group\. If a user belongs to more than one group assigned to the Studio, a union of permissions will be used for that user\.

**Prerequisites**
+ Make sure that you have enabled AWS SSO and configured your identity provider\. For more information, see [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md)\.
+ You must have the required permissions to create and manage an EMR Studio\. For more information, see [Add Required Permissions to Create and Manage an EMR Studio](emr-studio-admin-permissions.md)\.
+ Use **version 1\.18\.184** or later, or **version 2\.1\.4** or later of the AWS Command Line Interface if you want to assign users or groups to your Studio using the AWS CLI\.

------
#### [ Console ]

**To assign users or groups to an EMR Studio using the EMR console**

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **EMR Studio** from the left navigation\.

1. Choose your Studio name from the **Studios** list, or select the Studio and choose **View details**, to open the Studio detail page\.

1. Choose **Add Users** to see the **Users** and **Groups** search table\.

1. Select the **Users** tab or the **Groups** tab, and enter a search term in the search bar to find a user or group\. 

1. Select one or more users or groups from the search results list\. You can switch back and forth between the **Users** tab and the **Groups** tab\.

1. After you select users and groups to add to your Studio, choose **Add**\. You should see the users and groups appear in the **Studio users** list\. It might take a few seconds for the list to refresh\.

1. Follow the instructions in [Update Permissions for a User or Group Assigned to a Studio](#emr-studio-update-user) to refine the Studio permissions for a user or group\.

------
#### [ CLI ]

**To assign a user or group to an EMR Studio using the AWS CLI**

Insert your own values for the following `create-studio-session-mapping` arguments\. For more information about the `create-studio-session-mapping` command, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio-session-mapping.html](https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio-session-mapping.html)\.
+ **`--studio-id`** – The ID of the Studio to which you want to assign the user or group\. For instructions on how to retrieve your Studio ID, see [View Studio Details](emr-studio-manage-studio.md#emr-studio-get-studio-id)\.
+ `--identity-name` – The name of the user or group from the AWS SSO identity store\. For more information, see [UserName](https://docs.aws.amazon.com/singlesignon/latest/IdentityStoreAPIReference/API_User.html#singlesignon-Type-User-UserName) for users and [DisplayName](https://docs.aws.amazon.com/singlesignon/latest/IdentityStoreAPIReference/API_Group.html#singlesignon-Type-Group-DisplayName) for groups in the *AWS SSO Identity Store API Reference*\.
+ **`--identity-type`** – Use either `USER` or `GROUP` to specify the identity type\.
+ **`--session-policy-arn`** – The Amazon Resource Name \(ARN\) for the session policy you want to associate with the user or group\. For example, `arn:aws:iam::<aws-account-id>:policy/EMRStudio_Advanced_User_Policy`\. For more information, see [Create Session Policies for Studio Users and Groups](emr-studio-user-role.md#emr-studio-session-policies)\.

```
aws emr create-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-name <example-identity-name> \
 --identity-type <USER-or-GROUP> \
 --session-policy-arn <example-session-policy-arn>
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

Use the `get-studio-session-mapping` command to verify the new assignment\. Replace *<example\-identity\-name>* with the SSO name of the user or group that you updated\.

```
aws emr get-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-type <USER-or-GROUP> \
 --identity-name <user-or-group-name> \
```

------

## Update Permissions for a User or Group Assigned to a Studio<a name="emr-studio-update-user"></a>

You can change the set of Studio permissions \(session policy\) assigned to a user or group using the EMR console or the AWS CLI\.

------
#### [ Console ]

****To update EMR Studio permissions for a user or group using the console****

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **EMR Studio** from the left navigation\.

1. Choose your Studio name from the **Studios** list, or select the Studio and choose **View details**, to open the Studio detail page\.

1. In the** Studio users** list on the Studio detail page, search for the user or group you want to update\. You can search by name or identity type\.

1. Select the user or group that you want to update and choose **Assign policy** to open the **Session policy** dialog box\.

1. Select a policy to apply to the user or group that you chose in step 5, and choose **Apply policy**\. The **Studio users** list should display the policy name in the **Session policy** column for the user or group that you updated\.

------
#### [ CLI ]

**To update EMR Studio permissions for a user or group using the AWS CLI**

Insert your own values for the following `update-studio-session-mappings` arguments\. For more information about the `update-studio-session-mappings` command, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/update-studio-session-mapping.html](https://docs.aws.amazon.com/cli/latest/reference/emr/update-studio-session-mapping.html)\.

```
aws emr update-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-name <name-of-user-or-group-to-update> \
 --session-policy-arn <new-session-policy-arn-to-apply> \
 --identity-type <USER-or-GROUP> \
```

Use the `get-studio-session-mapping` command to verify the new session policy assignment\. Replace *<example\-identity\-name>* with the SSO name of the user or group that you updated\.

```
aws emr get-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-type <USER-or-GROUP> \
 --identity-name <user-or-group-name> \
```

------

## Remove a User or Group from a Studio<a name="emr-studio-remove-user"></a>

------
#### [ Console ]

****To remove a user or group from an EMR Studio using the console****

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **EMR Studio** from the left navigation\.

1. Choose your Studio name from the **Studios** list, or select the Studio and choose **View details**, to open the Studio detail page\.

1. In the** Studio users** list on the Studio detail page, find the user or group you want to remove from the Studio\. You can search by name or identity type\.

1. Select the user or group that you want to delete, choose **Delete** and confirm\. The user or group that you deleted disappears from the **Studio users** list\.

------
#### [ CLI ]

**To remove a user or group from an EMR Studio using the AWS CLI**

Insert your own values for the following `delete-studio-session-mapping` arguments\. For more information about the `delete-studio-session-mapping` command, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/delete-studio-session-mapping.html](https://docs.aws.amazon.com/cli/latest/reference/emr/delete-studio-session-mapping.html)\.

```
aws emr delete-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-type <USER-or-GROUP> \
 --identity-name <name-of-user-or-group-to-delete> \
```

------