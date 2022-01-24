# Configure Workspace collaboration<a name="emr-studio-workspace-collaboration"></a>

Workspace collaboration lets you write and run notebook code simultaneously with other members of your team\. When you work in the same notebook file, you'll see changes as your collaborators make them\. You can enable collaboration when you create a Workspace, or switch collaboration on and off in an existing Workspace\. 

**Prerequisites**

Before you configure collaboration for a Workspace, make sure you complete the following tasks:
+ Ensure that your EMR Studio admin has given you the necessary permissions\. For example, the following statement allows a user to configure collaboration for any Workspace with the tag key `creatorUserId` whose value matches the user's ID \(indicated by the policy variable `aws:userId`\)\.

  ```
  {
      "Sid": "UserRolePermissionsForCollaboration",
      "Action": [
          "elasticmapreduce:UpdateEditor",
          "elasticmapreduce:PutWorkspaceAccess",
          "elasticmapreduce:DeleteWorkspaceAccess",
          "elasticmapreduce:ListWorkspaceAccessIdentities"
      ],
      "Resource": "*",
      "Effect": "Allow",
      "Condition": {
          "StringEquals": {
              "elasticmapreduce:ResourceTag/creatorUserId": "${aws:userId}"
          }
      }
  }
  ```
+ Ensure that the service role associated with your EMR Studio has the permissions required to enable and configure Workspace collaboration, as in the poli following example statement\.

  ```
  {
      "Sid": "AllowWorkspaceCollaboration",
      "Effect": "Allow",
      "Action": [
          "iam:GetUser",
          "iam:GetRole",
          "iam:ListUsers",
          "iam:ListRoles",
          "sso:GetManagedApplicationInstance",
          "sso-directory:SearchUsers"
      ],
      "Resource": "*"
  }
  ```

  For more information, see [Create an EMR Studio service role](emr-studio-service-role.md)\.

**To enable Workspace collaboration and add collaborators**

1. In your Workspace, choose **Collaboration**\. 
**Note**  
You won't see the **Collaboration** panel unless your Studio administator has given you permission to configure collaboration for the Workspace\. For more information, see [Set ownership for Workspace collaboration](emr-studio-user-permissions.md#emr-studio-workspace-collaboration-permissions)\.

1. Make sure the **Allow Workspace collaboration** toggle is in the on position\. When you enable collaboration, only you and the collaborators that you add can see the Workspace in the list on the Studio **Workspaces **page\.

1. Enter a **Collaborator name**\. Your Workspace can have a maximum of five collaborators including yourself\. A collaborator can be any user with access to your EMR Studio\.

   The following table specifies the value to enter based on your EMR Studio authentication mode\. Your Studio admin can provide these values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-workspace-collaboration.html)

1. Choose **Add**\. The collaborator can now see the Workspace on their EMR Studio **Workspaces** page, and launch the Workspace to use it in real time with you\.

**Note**  
If you disable Workspace collaboration, the Workspace returns to its shared state and can be seen by all Studio users\. In the shared state, only one Studio user can open and work in the Workspace at a time\. 