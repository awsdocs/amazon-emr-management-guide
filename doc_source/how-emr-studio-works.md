# How Amazon EMR Studio works<a name="how-emr-studio-works"></a>

An Amazon EMR Studio is an Amazon EMR resource that you create for a team of users\. Each Studio is a self\-contained integrated development environment for Jupyter notebooks that run on Amazon EMR clusters\. Users log in to a Studio using corporate credentials\. 

Each EMR Studio that you create uses the following AWS resources: 
+ **An Amazon Virtual Private Cloud \(VPC\) with subnets **\- Users run Studio kernels and applications on Amazon EMR and Amazon EMR on EKS clusters in the specified VPC\. An EMR Studio can connect to any cluster in the subnets that you specify when you create the Studio\.
+ **IAM roles and permissions policies** \- To manage user permissions, you create IAM permissions policies that you attach to a user's IAM identity or to an IAM user role\. EMR Studio also uses an IAM service role and security groups to interoperate with other AWS services\. For more information, see [Configure EMR Studio security](emr-studio-security.md)\.
+ **Security groups** \- EMR Studio uses security groups to establish a secure network channel between the Studio and an EMR cluster\.
+ **An Amazon S3 backup location** \- EMR Studio saves notebook work in an Amazon S3 location\.

The following steps outline how to create and administer an EMR Studio:

1. Create a Studio in your AWS account with either IAM or AWS SSO authentication\. For instructions, see [Set up an Amazon EMR Studio](emr-studio-set-up.md)\.

1. Assign users and groups to your Studio\. Use permissions policies to set fine\-grained permissions for each user\. For more information, see the topic [Assign and manage EMR Studio users](emr-studio-manage-users.md)\.

1. Start monitoring EMR Studio actions with AWS CloudTrail events\. For more information, see [Monitor Amazon EMR Studio actions](emr-studio-manage-studio.md#emr-studio-monitor)\.

1. Provide more cluster options to Studio users with cluster templates and Amazon EMR on EKS managed endpoints\. For more information, see [Create AWS CloudFormation templates for Amazon EMR Studio](emr-studio-cluster-templates.md) and [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md)\.

## Authentication and user login<a name="emr-studio-login"></a>

Amazon EMR Studio supports two authentication modes: IAM authentication mode and SSO authentication mode\. IAM mode uses AWS Identity and Access Management \(IAM\), while SSO mode uses AWS Single Sign\-On \(AWS SSO\)\. When you create an EMR Studio, you choose the authentication mode for all users of that Studio\. For more information, see [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\.

The authentication mode that you choose for Amazon EMR Studio also affects how users log in to a Studio, how you assign a user to a Studio, and how you *authorize* \(give permissions to\) users to perform actions such as creating new Amazon EMR clusters\. For more information, see [Login, user assignment, and user permissions](emr-studio-authentication.md#emr-studio-login-auth-differences)\.

## Access control<a name="emr-studio-access-control"></a>

In Amazon EMR Studio, you configure user authorization \(permissions\) with AWS Identity and Access Management \(IAM\) identity\-based policies\. In these policies, you specify the Studio actions that a user or group can perform\.

The way you *apply* a policy to a user differs by authentication mode\. 
+ With IAM authentication or federation, you create one or more IAM permissions policies for EMR Studio and apply the policies directly to IAM identities \(users, groups, or roles\) for EMR Studio\. 
+ With AWS SSO authentication, you create a single EMR Studio user role with session policies\. Then, when you [assign users and groups](emr-studio-manage-users.md#emr-studio-assign-users-groups) to a Studio, you map a session policy to that user or group to apply the permissions\.

For more information, see [Configure EMR Studio user permissions](emr-studio-user-permissions.md)\.

## Workspaces<a name="emr-studio-workspaces"></a>

Workspaces are the primary building blocks of Amazon EMR Studio\. To organize notebooks, users create one or more Workspaces in a Studio\. 

Similar to [workspaces in JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/urls.html#managing-workspaces-ui), a Workspace preserves the state of notebook work\. However, the Workspace user interface extends the open\-source [JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) interface with additional tools to let you create and attach EMR clusters, run jobs, explore sample notebooks, and link Git repositories\.

The following list includes key features of EMR Studio Workspaces:
+ Workspace visibility is Studio\-based\. Workspaces that you create in one Studio aren't visible in other Studios\.
+ Notebooks in a Workspace share the same EMR cluster to run commands\. You can attach a Workspace to an Amazon EMR cluster running on Amazon EC2 or to an Amazon EMR on EKS virtual cluster and managed endpoint\.
+ Workspaces can switch over to another Availability Zone that you associate with a Studio's subnets\. You can stop and restart a Workspace to prompt the failover process\. When you restart a Workspace, EMR Studio launches the Workspace in a different Availability Zone in the Studio's VPC when the Studio is configured with access to multiple Availability Zones\. If the Studio has only one Availability Zone, EMR Studio attempts to launch the Workspace in a different subnet\. For more information, see [Resolve Workspace connectivity issues](emr-studio-configure-workspace.md#emr-studio-workspace-stop-start)\.
+ A Workspace can connect to clusters in any of the subnets that are associated with a Studio\.

For more information about creating and configuring EMR Studio Workspaces, see [Workspaces in Amazon EMR Studio](emr-studio-configure-workspace.md)\.

## Notebook storage in Amazon EMR Studio<a name="emr-studio-storage"></a>

When you use a Workspace, EMR Studio autosaves the cells in notebook files at a regular cadence in the Amazon S3 location that is associated with your Studio\. This backup process preserves work between sessions so that you can come back to it later without committing changes to a Git repository\. For more information, see [Save Workspace content](emr-studio-configure-workspace.md#emr-studio-save-workspace)\.

When you delete a notebook file from a Workspace, EMR Studio deletes the backup version from Amazon S3 for you\. However, if you delete a Workspace without first deleting its notebook files, the notebook files remain in Amazon S3 and continue to accrue storage charges\. To learn more, see [Delete a Workspace and notebook files](emr-studio-configure-workspace.md#emr-studio-delete-workspace)\.