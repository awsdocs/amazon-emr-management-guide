# Assign a User or Group to Your EMR Studio<a name="emr-studio-assign-users-groups"></a>

You can assign users and groups to an EMR Studio using the `create-studio-session-mapping` AWS CLI command\. When you assign a user or group, you must specify a session policy that defines fine\-grained permissions, such as the ability to create a new EMR cluster, for that user or group\. Amazon EMR stores these session policy mappings\.

**Prerequisites**
+ Make sure that you have enabled AWS SSO and imported users and groups from your identity provider\. For more information, see [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md)\.
+ Use the AWS Command Line Interface **version 1\.18\.184** or later, or **version 2\.1\.4** or later\.

**To assign a user or group to your EMR Studio using the AWS CLI**

Insert your own values for the following `create-studio-session-mapping` arguments\. For more information about the `create-studio-session-mapping` command, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio-session-mapping.html](https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio-session-mapping.html)\.
+ **`--studio-id`** – The ID of the Studio to which you want to assign the user or group\. For instructions on how to retrieve your Studio ID, see [Fetch Your EMR Studio ID](emr-studio-get-studio-id.md)\.
+ `--identity-name` – The name of the user or group from the AWS SSO identity store\. For more information, see [UserName](https://docs.aws.amazon.com/singlesignon/latest/IdentityStoreAPIReference/API_User.html#singlesignon-Type-User-UserName) for users and [DisplayName](https://docs.aws.amazon.com/singlesignon/latest/IdentityStoreAPIReference/API_Group.html#singlesignon-Type-Group-DisplayName) for groups in the *AWS SSO Identity Store API Reference*\.
+ **`--identity-type`** – Use either `USER` or `GROUP` to specify the identity type\.
+ **`--session-policy-arn`** – The Amazon Resource Name \(ARN\) for the session policy you want to associate with the user or group\. For example, `arn:aws:iam::<aws-account-id>:policy/EMRStudio_Advanced_User_Policy`\. For more information, see [Step 1: Create Session Policies for Studio Users and Groups](emr-studio-user-role.md#emr-studio-session-policies)\.
**Note**  
If a user belongs to more than one group assigned to the Studio, a union of permissions will be used for that user\.

```
aws emr create-studio-session-mapping \
 --studio-id <example-studio-id> \
 --identity-name <example-identity-name> \
 --identity-type <USER-or-GROUP> \
 --session-policy-arn <example-policy-arn>
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

For more information about how to manage users and groups assigned to a Studio, see the following topics in the *AWS CLI Command Reference*:
+ [update\-studio\-session\-mapping](https://docs.aws.amazon.com/cli/latest/reference/emr/update-studio-session-mapping.html)
+ [list\-studio\-session\-mappings](https://docs.aws.amazon.com/cli/latest/reference/emr/list-studio-session-mappings.html)
+ [delete\-studio\-session\-mapping](https://docs.aws.amazon.com/cli/latest/reference/emr/delete-studio-session-mapping.html)