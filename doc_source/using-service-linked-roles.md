# Using the Service\-Linked Role for Amazon EMR<a name="using-service-linked-roles"></a>

Amazon EMR uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Amazon EMR\. The service\-linked role is predefined by Amazon EMR and includes the permissions that Amazon EMR requires to call Amazon EC2 on your behalf to clean up cluster resources after they are no longer in use\. The service\-linked role works together with the Amazon EMR service role and Amazon EC2 instance profile for Amazon EMR\. For more information about the service role and instance profile, see [Configure IAM Roles for Amazon EMR Permissions to AWS Services](emr-iam-roles.md)\.

Amazon EMR defines the permissions of this service\-linked role, and unless defined otherwise, only Amazon EMR can assume the role\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\. You can delete the role only after you terminate all EMR clusters in the account\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-Linked Role Permissions for Amazon EMR<a name="service-linked-role-permissions"></a>

Amazon EMR uses the **AWSServiceRoleForEMRCleanup** role, which is a service\-based role that allows Amazon EMR to terminate and delete Amazon EC2 resources on your behalf if the Amazon EMR service role has lost that ability\. Amazon EMR creates the role automatically during cluster creation if it does not already exist\.

The AWSServiceRoleForEMRCleanup service\-linked role trusts the following services to assume the role:
+ `elasticmapreduce.amazonaws.com`

The AWSServiceRoleForEMRCleanup service\-linked role permissions policy allows Amazon EMR to complete the following actions on the specified resources:
+ Action: `DescribeInstances` on `ec2`
+ Action: `DescribeSpotInstanceRequests` on `ec2`
+ Action: `ModifyInstanceAttribute` on `ec2`
+ Action: `TerminateInstances` on `ec2`
+ Action: `CancelSpotInstanceRequests` on `ec2`
+ Action: `DeleteNetworkInterface` on `ec2`
+ Action: `DescribeInstanceAttribute` on `ec2`
+ Action: `DescribeVolumeStatus` on `ec2`
+ Action: `DescribeVolumes` on `ec2`
+ Action: `DetachVolume` on `ec2`
+ Action: `DeleteVolume` on `ec2`

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\.

**To allow an IAM entity to create the AWSServiceRoleForEMRCleanup service\-linked role**

Add the following statement to the permissions policy for the IAM entity that needs to create the service\-linked role:

```
{
    "Effect": "Allow",
    "Action": [
        "iam:CreateServiceLinkedRole",
        "iam:PutRolePolicy"
    ],
    "Resource": "arn:aws:iam::*:role/aws-service-role/elasticmapreduce.amazonaws.com*/AWSServiceRoleForEMRCleanup*",
    "Condition": {
        "StringLike": {
            "iam:AWSServiceName": [
                "elasticmapreduce.amazonaws.com",
                "elasticmapreduce.amazonaws.com.cn"
            ]
        }
    }
}
```

**To allow an IAM entity to edit the description of the AWSServiceRoleForEMRCleanup service\-linked role**

Add the following statement to the permissions policy for the IAM entity that needs to edit the description of a service\-linked role:

```
{
    "Effect": "Allow",
    "Action": [
        "iam:UpdateRoleDescription"
    ],
    "Resource": "arn:aws:iam::*:role/aws-service-role/elasticmapreduce.amazonaws.com*/AWSServiceRoleForEMRCleanup*",
    "Condition": {
        "StringLike": {
            "iam:AWSServiceName": [
                "elasticmapreduce.amazonaws.com",
                "elasticmapreduce.amazonaws.com.cn"
            ]
        }
    }
}
```

**To allow an IAM entity to delete the AWSServiceRoleForEMRCleanup service\-linked role**

Add the following statement to the permissions policy for the IAM entity that needs to delete a service\-linked role:

```
{
    "Effect": "Allow",
    "Action": [
        "iam:DeleteServiceLinkedRole",
        "iam:GetServiceLinkedRoleDeletionStatus"
    ],
    "Resource": "arn:aws:iam::*:role/aws-service-role/elasticmapreduce.amazonaws.com*/AWSServiceRoleForEMRCleanup*",
    "Condition": {
        "StringLike": {
            "iam:AWSServiceName": [
                "elasticmapreduce.amazonaws.com",
                "elasticmapreduce.amazonaws.com.cn"
            ]
        }
    }
}
```

## Creating a Service\-Linked Role for Amazon EMR<a name="create-service-linked-role"></a>

You don't need to manually create the AWSServiceRoleForEMRCleanup role\. When you launch a cluster, either for the first time or when a service\-linked role is not present, Amazon EMR creates the service\-linked role for you\. You must have permissions to create the service\-linked role\. For an example statement that adds this capability to the permissions policy of an IAM entity \(such as a user, group, or role\), see [Service\-Linked Role Permissions for Amazon EMR](#service-linked-role-permissions)\.

**Important**  
If you were using Amazon EMR before October 24, 2017, when service\-linked roles were not supported, then Amazon EMR created the AWSServiceRoleForEMRCleanup role in your account\. For more information, see [A New Role Appeared in My IAM Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared)\.

## Editing a Service\-Linked Role for Amazon EMR<a name="edit-service-linked-role"></a>

Amazon EMR does not allow you to edit the AWSServiceRoleForEMRCleanup service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\.

### Editing a Service\-Linked Role Description \(IAM Console\)<a name="edit-service-linked-role-iam-console"></a>

You can use the IAM console to edit the description of a service\-linked role\.

**To edit the description of a service\-linked role \(console\)**

1. In the navigation pane of the IAM console, choose **Roles**\.

1. Choose the name of the role to modify\.

1. To the far right of **Role description**, choose **Edit**\. 

1. Type a new description in the box and choose **Save**\.

### Editing a Service\-Linked Role Description \(IAM CLI\)<a name="edit-service-linked-role-iam-cli"></a>

You can use IAM commands from the AWS Command Line Interface to edit the description of a service\-linked role\.

**To change the description of a service\-linked role \(CLI\)**

1. \(Optional\) To view the current description for a role, use the following commands:

   ```
   $ aws iam [get\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/get-role.html) --role-name role-name
   ```

   Use the role name, not the ARN, to refer to roles with the CLI commands\. For example, if a role has the following ARN: `arn:aws:iam::123456789012:role/myrole`, you refer to the role as **myrole**\.

1. To update a service\-linked role's description, use one of the following commands:

   ```
   $ aws iam [update\-role\-description](https://docs.aws.amazon.com/cli/latest/reference/iam/update-role-description.html) --role-name role-name --description description
   ```

### Editing a Service\-Linked Role Description \(IAM API\)<a name="edit-service-linked-role-iam-api"></a>

You can use the IAM API to edit the description of a service\-linked role\.

**To change the description of a service\-linked role \(API\)**

1. \(Optional\) To view the current description for a role, use the following command:

   IAM API: [GetRole](https://docs.aws.amazon.com/IAM/latest/APIReference/API_GetRole.html) 

1. To update a role's description, use the following command: 

   IAM API: [UpdateRoleDescription](https://docs.aws.amazon.com/IAM/latest/APIReference/API_UpdateRoleDescription.html)

## Deleting a Service\-Linked Role for Amazon EMR<a name="delete-service-linked-role"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way, you don’t have an unused entity that is not being actively monitored or maintained\. However, you must clean up your service\-linked role before you can delete it\.

### Cleaning Up a Service\-Linked Role<a name="service-linked-role-review-before-delete"></a>

Before you can use IAM to delete a service\-linked role, you must first confirm that the role has no active sessions and remove any resources used by the role\.

**To check whether the service\-linked role has an active session in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. Select the name \(not the check box\) of the AWSServiceRoleForEMRCleanup role\.

1. On the **Summary** page for the selected role, choose **Access Advisor**\.

1. On the **Access Advisor** tab, review the recent activity for the service\-linked role\.
**Note**  
If you are unsure whether Amazon EMR is using the AWSServiceRoleForEMRCleanup role, you can try to delete the role\. If the service is using the role, then the deletion fails and you can view the regions where the role is being used\. If the role is being used, then you must wait for the session to end before you can delete the role\. You cannot revoke the session for a service\-linked role\. 

**To remove Amazon EMR resources used by the AWSServiceRoleForEMRCleanup**
+ Terminate all clusters in your account\. For more information, see [Terminate a Cluster](UsingEMR_TerminateJobFlow.md)\.

### Deleting a Service\-Linked Role \(IAM Console\)<a name="delete-service-linked-role-iam-console"></a>

You can use the IAM console to delete a service\-linked role\.

**To delete a service\-linked role \(console\)**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. Select the check box next to AWSServiceRoleForEMRCleanup, not the name or row itself\. 

1. For **Role actions** at the top of the page, choose **Delete role**\.

1. In the confirmation dialog box, review the service last accessed data, which shows when each of the selected roles last accessed an AWS service\. This helps you to confirm whether the role is currently active\. To proceed, choose **Yes, Delete**\.

1. Watch the IAM console notifications to monitor the progress of the service\-linked role deletion\. Because the IAM service\-linked role deletion is asynchronous, after you submit the role for deletion, the deletion task can succeed or fail\. If the task fails, you can choose **View details** or **View Resources** from the notifications to learn why the deletion failed\. If the deletion fails because there are resources in the service that are being used by the role, then the reason for the failure includes a list of resources\.

### Deleting a Service\-Linked Role \(IAM CLI\)<a name="delete-service-linked-role-iam-cli"></a>

You can use IAM commands from the AWS Command Line Interface to delete a service\-linked role\. Because a service\-linked role cannot be deleted if it is being used or has associated resources, you must submit a deletion request\. If these conditions are not met, that request can be denied\. 

**To delete a service\-linked role \(CLI\)**

1. To check the status of the deletion task, you must capture the `deletion-task-id` from the response\. Type the following command to submit a service\-linked role deletion request:

   ```
   $ aws iam [delete\-service\-linked\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-service-linked-role.html) --role-name AWSServiceRoleForEMRCleanup
   ```

1. Type the following command to check the status of the deletion task:

   ```
   $ aws iam [get\-service\-linked\-role\-deletion\-status](https://docs.aws.amazon.com/cli/latest/reference/iam/get-service-linked-role-deletion-status.html) --deletion-task-id deletion-task-id
   ```

   The status of the deletion task can be `NOT_STARTED`, `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\. If the deletion fails, the call returns the reason that it failed so that you can troubleshoot\.

### Deleting a Service\-Linked Role \(IAM API\)<a name="delete-service-linked-role-iam-api"></a>

You can use the IAM API to delete a service\-linked role\. Because a service\-linked role cannot be deleted if it is being used or has associated resources, you must submit a deletion request\. If these conditions are not met, that request can be denied\. 

**To delete a service\-linked role \(API\)**

1. To submit a deletion request for a service\-linked role, call [DeleteServiceLinkedRole](https://docs.aws.amazon.com/IAM/latest/APIReference/API_DeleteServiceLinkedRole.html)\. In the request, specify the AWSServiceRoleForEMRCleanup role name\.

   To check the status of the deletion task, you must capture the `DeletionTaskId` from the response\.

1. To check the status of the deletion, call [GetServiceLinkedRoleDeletionStatus](https://docs.aws.amazon.com/IAM/latest/APIReference/API_GetServiceLinkedRoleDeletionStatus.html)\. In the request, specify the `DeletionTaskId`\.

   The status of the deletion task can be `NOT_STARTED`, `IN_PROGRESS`, `SUCCEEDED`, or `FAILED`\. If the deletion fails, the call returns the reason that it failed so that you can troubleshoot\.

## Supported Regions for Amazon EMR Service\-Linked Roles<a name="emr-slr-regions"></a>

Amazon EMR supports using service\-linked roles in the following regions\.


****  

| Region name | Region identity | Support in Amazon EMR | 
| --- | --- | --- | 
| US East \(N\. Virginia\) | us\-east\-1 | Yes | 
| US East \(Ohio\) | us\-east\-2 | Yes | 
| US West \(N\. California\) | us\-west\-1 | Yes | 
| US West \(Oregon\) | us\-west\-2 | Yes | 
| Asia Pacific \(Mumbai\) | ap\-south\-1 | Yes | 
| Asia Pacific \(Osaka\-Local\) | ap\-northeast\-3 | Yes | 
| Asia Pacific \(Seoul\) | ap\-northeast\-2 | Yes | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | Yes | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | Yes | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | Yes | 
| Canada \(Central\) | ca\-central\-1 | Yes | 
| EU \(Frankfurt\) | eu\-central\-1 | Yes | 
| EU \(Ireland\) | eu\-west\-1 | Yes | 
| EU \(London\) | eu\-west\-2 | Yes | 
| EU \(Paris\) | eu\-west\-3 | Yes | 
| South America \(São Paulo\) | sa\-east\-1 | Yes | 