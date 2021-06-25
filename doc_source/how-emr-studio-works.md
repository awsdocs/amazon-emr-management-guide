# How Amazon EMR Studio works<a name="how-emr-studio-works"></a>

Before your team can use Amazon EMR Studio, you must set up a Studio\. To set up a Studio, as well as assign users and groups to it, you connect your identity provider \(IdP\) to AWS Single Sign\-On \(SSO\) and enable SSO for your AWS Organizations\. After you set up a Studio, your team can log in using the Studio access URL and their corporate credentials\. For a complete list of instructions, see [Set up and manage an Amazon EMR Studio](emr-studio-set-up.md)\.

When you set up a Studio, you associate it with a set of AWS resources such as an Amazon Virtual Private Cloud \(VPC\) and subnets in that VPC\. When logged in to a Studio, a user can only access Amazon EMR and Amazon EMR on EKS clusters in the specified VPC and subnets\. 

Each Studio uses an IAM service role and security groups to interoperate with other AWS services and establish a secure network channel between the Studio and an EMR cluster\. To control the actions that a Studio user can take, EMR Studio uses an IAM user role in combination with IAM session policies that you manage\. For more information, see [EMR Studio security and access control](emr-studio-security.md)\.

## Workspaces in Amazon EMR Studio<a name="emr-studio-workspaces"></a>

Workspaces are the primary building blocks of Amazon EMR Studio\. When you log in to EMR Studio, you can create Workspaces to help you organize and run your notebooks\. Workspaces are similar in some ways to [workspaces in JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/urls.html#managing-workspaces-ui), since they preserve the state of your notebook work\. The Workspace user interface extends the open\-source [JupyterLab](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) interface with additional tools to help you create and attach EMR clusters, run jobs, explore sample notebooks, and link Git repositories\.

After you configure a Workspace, all of the notebooks in the Workspace share the following user\-defined properties:
+ **Amazon VPC subnet** – You must associate a Workspace with one of your Studio's subnets\. A Workspace can only access EMR clusters in its associated subnet\. When you create a new EMR cluster from a Workspace, the cluster is launched in the same subnet as the Workspace\. 
+ **EMR cluster** – Amazon EMR Studio runs notebook commands using a kernel on an EMR cluster\. You can attach a Workspace to an Amazon EMR cluster running on Amazon EC2 or to a virtual cluster created using Amazon EMR on EKS, and switch clusters without closing the Workspace\. 

Workspace visibility is Studio\-based\. Workspaces that you create in one Studio are not visible in other Studios\. For more information about creating and configuring EMR Studio Workspaces, see [Workspaces in Amazon EMR Studio](emr-studio-configure-workspace.md)\.

## Notebook storage in Amazon EMR Studio<a name="emr-studio-storage"></a>

When you use a Workspace, EMR Studio periodically auto\-saves the cells in your notebook files in the Amazon S3 location associated with your Studio\. This backup process also preserves your work between sessions without the need to link your Workspace to a Git repository\. For more information, see [Save Workspace content](emr-studio-configure-workspace.md#emr-studio-save-workspace)\.

When you delete a notebook file from a Workspace, EMR Studio deletes the backup version from Amazon S3 for you\. However, if you delete an entire Workspace without first deleting its notebook files, the notebook files remain in Amazon S3 and continue to accrue storage charges\. To learn more, see [Delete a Workspace and Notebook files](emr-studio-configure-workspace.md#emr-studio-delete-workspace)\.