# How Amazon EMR Studio works<a name="how-emr-studio-works"></a>

An Amazon EMR Studio is an Amazon EMR resource that you create for a team of users\. Each Studio is a self\-contained integrated development environment for Jupyter notebooks that run on Amazon EMR clusters\. Users log in to a Studio using corporate credentials\. 

Each EMR Studio that you create uses the following AWS resources: 
+ **An Amazon Virtual Private Cloud \(VPC\) with subnets **\- Users run Studio kernels and applications on Amazon EMR and Amazon EMR on EKS clusters in the specified VPC\. An EMR Studio can connect to any cluster in the subnets that you specify when you create the Studio\.
+ **IAM roles and permissions policies** \- To manage user permissions, you create IAM permissions policies that you attach to a user's IAM identity or to an IAM user role\. EMR Studio also uses an IAM service role and security groups to interoperate with other AWS services\. For more information, see [Access control](#emr-studio-access-control) and [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)\.
+ **Security groups** \- EMR Studio uses security groups to establish a secure network channel between the Studio and an EMR cluster\.
+ **An Amazon S3 backup location** \- EMR Studio saves notebook work in an Amazon S3 location\.

The following steps outline how to create and administer an EMR Studio:

1. Create a Studio in your AWS account with either IAM or AWS SSO authentication\. For instructions, see [Set up an EMR Studio](emr-studio-set-up.md)\.

1. Assign users and groups to your Studio\. Use permissions policies to set fine\-grained permissions for each user\. For more information, see the topic [Assign and manage EMR Studio users](emr-studio-manage-users.md)\.

1. Start monitoring EMR Studio actions with AWS CloudTrail events\. For more information, see [Monitor Amazon EMR Studio actions](emr-studio-manage-studio.md#emr-studio-monitor)\.

1. Provide more cluster options to Studio users with cluster templates and Amazon EMR on EKS managed endpoints\. For more information, see [Create AWS CloudFormation templates for Amazon EMR Studio](emr-studio-cluster-templates.md) and [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md)\.

## Authentication and user login<a name="emr-studio-login"></a>

Amazon EMR Studio supports two authentication modes: IAM authentication mode and SSO authentication mode\. IAM mode uses AWS Identity and Access Management \(IAM\), while SSO mode uses AWS Single Sign\-On \(AWS SSO\)\. When you create an EMR Studio, you choose the authentication mode for all users of that Studio\.

### IAM authentication mode<a name="emr-studio-login-iam-mode"></a>

With IAM authentication mode, you can use IAM authentication or IAM federation\. 

IAM *authentication* lets you manage IAM identities such as users, groups, and roles in IAM\. You grant users access to a Studio with IAM permissions policies and [attribute\-based access control \(ABAC\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html)\. 

IAM *federation* lets you establish trust between a third\-party identity provider \(IdP\) and AWS so that you can manage user identities through your IdP\.

### AWS SSO authentication mode<a name="emr-studio-login-sso-mode"></a>

SSO authentication mode lets you give users federated access to an EMR Studio\. You can use AWS SSO to authenticate users and groups from your AWS SSO directory, your existing corporate directory, or an external IdP such as Azure Active Directory \(AD\)\. You then manage users with your identity provider \(IdP\)\.

EMR Studio supports using the following identity providers for AWS SSO:
+ **AWS Managed Microsoft AD and self\-managed Active Directory** – For more information, see [Connect to your Microsoft AD directory](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-ad.html)\.
+ **SAML\-based providers** – For a full list, see [Supported identity providers](https://docs.aws.amazon.com/singlesignon/latest/userguide/supported-idps.html)\.
+ **The AWS Single Sign\-On store** – For more information, see [Manage identities in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-sso.html)\.

### How authentication affects login and user assignment<a name="emr-studio-login-auth-differences"></a>

The authentication mode that you choose for Amazon EMR Studio affects how users log in to a Studio, how you assign a user to a Studio, and how you *authorize* \(give permissions to\) users to perform actions such as creating new Amazon EMR clusters\.

The following table summarizes login methods for EMR Studio according to authentication mode\.


**Amazon EMR Studio login options by authentication mode**  

| Authentication mode | Login method | Description | 
| --- | --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/how-emr-studio-works.html)  | EMR Studio URL |  Users log in to a Studio using the Studio access URL\. For example, `https://xxxxxxxxxxxxxxxxxxxxxxx.emrstudio-prod.us-east-1.amazonaws.com`\.  Users enter IAM credentials when you use IAM authentication\. When you use IAM federation or AWS SSO, EMR Studio redirects users to your identity provider's sign\-in URL to enter credentials\. In the context of identity federation, this login option is called Service Provider \(SP\) initiated sign\-in\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/how-emr-studio-works.html)  | Identity provider \(IdP\) portal |  Users log in to your identity provider's portal, such as the Azure portal, and launch the Amazon EMR console\. After launching the Amazon EMR console, users select and open a Studio from the **Studios list**\. You can also configure EMR Studio as a SAML application so that users can log in to a specific Studio from your identity provider's portal\. For instructions, see [To configure an EMR Studio as a SAML application in your IdP portal](emr-studio-authentication.md#emr-studio-create-federation-deeplink)\. In the context of identity federation, this login option is called identity provider \(IdP\) initiated sign\-in\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/how-emr-studio-works.html)  | AWS Management Console | Users sign in to the AWS Management Console using IAM credentials and open a Studio from the Studios list in the Amazon EMR console\. | 

The following table outlines user assignment and authorization for Amazon EMR Studio by authentication mode\.


**Amazon EMR Studio user assignment and authorization by authentication mode**  

| Authentication mode | User assignment | User authorization | 
| --- | --- | --- | 
|  IAM \(authentication and federation\)  |  Allow the `CreateStudioPresignedUrl` action in an IAM permissions policy attached to an IAM identity \(user, group, or role\)\.  For federated users, allow the `CreateStudioPresignedUrl` action in an IAM in the permissions policy that you configure for the IAM role you use for federation\. Use attribute\-based access control \(ABAC\) to specify the Studio or Studios that the user can access\. For instructions, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.  |  Define IAM permissions policies that allow certain EMR Studio actions\.  For native IAM users, attach the IAM permissions policy to an IAM identity \(user, group, or role\)\. For federated users, allow Studio actions in the permissions policy that you configure for the IAM role you use for federation\. For more information, see [Configure EMR Studio user permissions](emr-studio-user-permissions.md)\.  | 
| AWS SSO |  Assign a user to a Studio by mapping the user to a Studio with a specified session policy\. For instructions, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.  |  Define IAM session policies that allow certain EMR Studio actions\. Map a session policy to a user when you assign the user to a Studio \. For more information, see [User permissions for AWS SSO authentication mode](#emr-studio-sso-authorization)\.  | 

## Access control<a name="emr-studio-access-control"></a>

In Amazon EMR Studio, you configure user authorization \(permissions\) with AWS Identity and Access Management \(IAM\) identity\-based policies\. In these policies, you specify allowed actions and resources, as well as the conditions under which the actions are allowed\.

### User permissions for IAM authentication mode<a name="emr-studio-iam-authorization"></a>

To set user permissions when you use IAM authentication for EMR Studio, you allow actions such as `elasticmapreduce:RunJobFlow` in an IAM permissions policy\. You can create one or more permissions policies to use\. For example, you might create a basic policy that does not allow a user to create new Amazon EMR clusters, and another policy that does allow cluster creation\. For a list of all Studio actions, see [AWS Identity and Access Management permissions for EMR Studio users](emr-studio-user-permissions.md#emr-studio-iam-permissions-table)\.

### User permissions for AWS SSO authentication mode<a name="emr-studio-sso-authorization"></a>

When you use AWS SSO authentication, you create a single EMR Studio user role\. The *user role* is a dedicated IAM role that a Studio assumes when a user logs in\.

You attach IAM session policies to the EMR Studio user role\. A *session policy* is a special kind of IAM permissions policy that limits what a federated user can do during a Studio login session\. Session policies let you set specific permissions for a user or group without creating multiple user roles for EMR Studio\.

When you [assign users and groups](emr-studio-manage-users.md#emr-studio-assign-users-groups) to a Studio, you map a session policy to that user or group to apply fine\-grained permissions\. You can also update a user or group's session policy at any time\. Amazon EMR stores each session policy mapping that you create\.

For more information about session policies, see [Policies and permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_session) in the *AWS Identity and Access Management User Guide*\. 

## Workspaces<a name="emr-studio-workspaces"></a>

Workspaces are the primary building blocks of Amazon EMR Studio\. To organize notebooks, users create one or more Workspaces in a Studio\. 

Similar to [workspaces in JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/urls.html#managing-workspaces-ui), a Workspace preserves the state of notebook work\. However, the Workspace user interface extends the open\-source [JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) interface with additional tools to let you create and attach EMR clusters, run jobs, explore sample notebooks, and link Git repositories\.

The following list includes key features of EMR Studio Workspaces:
+ Workspace visibility is Studio\-based\. Workspaces that you create in one Studio aren't visible in other Studios\.
+ Notebooks in a Workspace share the same EMR cluster to run commands\. You can attach a Workspace to an Amazon EMR cluster running on Amazon EC2 or to an Amazon EMR on EKS virtual cluster and managed endpoint\.
+ Workspaces can switch over to another Availability Zone that you associate with a Studio's subnets\. You can stop and restart a Workspace to prompt the failover process\. When you restart a Workspace, EMR Studio launches the Workspace in a different Availability Zone in the Studio's VPC when the Studio is configured with access to multiple Availability Zones\. If the Studio has only one Availability Zone, EMR Studio attempts to launch the Workspace in a different subnet\. For more information, see [Resolve Workspace connectivity issues](emr-studio-configure-workspace.md#emr-studio-workspace-stop-start)\.
+ A Workspace can connect to clusters in any of the subnets that are associated with a Studio\.

For more information about creating and configuring EMR Studio Workspaces, see [Learn Workspace basics](emr-studio-configure-workspace.md)\.

## Notebook storage in Amazon EMR Studio<a name="emr-studio-storage"></a>

When you use a Workspace, EMR Studio autosaves the cells in notebook files at a regular cadence in the Amazon S3 location that is associated with your Studio\. This backup process preserves work between sessions so that you can come back to it later without committing changes to a Git repository\. For more information, see [Save Workspace content](emr-studio-configure-workspace.md#emr-studio-save-workspace)\.

When you delete a notebook file from a Workspace, EMR Studio deletes the backup version from Amazon S3 for you\. However, if you delete a Workspace without first deleting its notebook files, the notebook files remain in Amazon S3 and continue to accrue storage charges\. To learn more, see [Delete a Workspace and notebook files](emr-studio-configure-workspace.md#emr-studio-delete-workspace)\.