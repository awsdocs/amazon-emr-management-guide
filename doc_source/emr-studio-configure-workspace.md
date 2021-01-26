# Configure a Workspace for EMR Studio<a name="emr-studio-configure-workspace"></a>

When you are logged in to an Amazon EMR Studio, you can create and configure different *Workspaces* to organize and run notebooks\. This section provides instructions for creating and working with Workspaces\. For a conceptual overview, see [Workspaces in Amazon EMR Studio](how-emr-studio-works.md#emr-studio-workspaces)\.

## Understand Workspace Status<a name="emr-studio-workspace-status"></a>

After you create an EMR Studio Workspace, it will appear as a row in the **Workspaces** list with its name, status, creation time, and last modified timestamp\. The following table describes the possible Workspace statuses\.


****  

| Status | Meaning | 
| --- | --- | 
| Starting | The Workspace is being prepared, but is not yet ready to use\. You can't open a Workspace when its status is Starting\. | 
| Ready | You can open the Workspace to use the notebook editor\. When a Workspace has a Ready status, you can open or delete it\. | 
| Attaching | The Workspace is being attached to a cluster\. | 
| Attached | The Workspace is already attached to an EMR cluster\. If a Workspace's status is not Attached, you must attach it to a cluster before you can run notebook code\. | 
| Idle | The Workspace is stopped and currently idle\. To reactivate an idle Workspace, select it from the Workspaces list\. The status changes from Idle to Starting to Ready when you select the Workspace\. | 
| Stopping | The Workspace is being stopped and will be set to Idle\. EMR Studio stops notebooks that have been inactive for a long time\. | 
| Deleting | When you delete a Workspace, EMR Studio marks it for deletion and starts the deletion process\. After the deletion process completes, the Workspace disappears from the list\. | 

## Create an EMR Studio Workspace<a name="emr-studio-create-workspace"></a>

You can create EMR Studio Workspaces to run notebook code using the EMR Studio interface\. 

**To create a Workspace in an EMR Studio**

1. Log in to your EMR Studio using the Studio access URL\. For example, *https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxx\.emrstudio\-prod\.us\-east\-1\.amazonaws\.com*\. 

1. Launch the **Create a Workspace** dialog box\. There are three ways to start the Workspace creation dialog box in an EMR Studio\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-configure-workspace.html)

1. Enter a **Workspace name** and a **Description**\. Naming your Workspace helps you identify it on the **Workspaces** page\.

1. Expand the **Subnet** dropdown list and select a subnet for your Workspace\. Each subnet in the dropdown list belongs to the same Amazon Virtual Private Cloud \(VPC\) as your Studio\.

1. Under **S3 location**, choose **Browse S3** to open the bucket selection dialog box\. Search for an S3 bucket by name, select it, and select **Choose**\. For more information, see [Notebook Storage in Amazon EMR Studio](how-emr-studio-works.md#emr-studio-storage)\.

1. \(Optional\) To attach a cluster to your Workspace when you create the Workspace, expand the **Advanced configuration** section\. 
**Note**  
You must select a subnet for your Workspace before you can choose a cluster option under **Advanced configuration**\.   
Provisioning a new cluster requires access permissions from your administrator\. 

   Choose one of the cluster options for your Workspace and attach the cluster\. For more information about provisioning a cluster when you create a Workspace, see [Create a New EMR Cluster to Attach to Your Workspace](emr-studio-create-use-clusters.md#emr-studio-create-cluster)\.

1. Choose **Create Workspace** in the lower right of the page\. 

After you create your Workspace, EMR Studio will open the **Workspaces** page\. You will see a green success banner at the top of the page and can find your newly\-created Workspace in the list\.

## Launch a Workspace<a name="emr-studio-use-workspace"></a>

To start working with notebook files, launch a Workspace to access the notebook editor\. The Workspace page of a Studio lists all of the Workspaces created in that Studio with details including **Name**, **Status**, **Creation time**, and **Last modified**\. 

**To launch your Workspace for editing and running notebooks**

1. On the **Workspaces** page of your Studio, find your Workspace\. You can filter the list by keyword or by column value\.

1. Choose your Workspace name to launch the Workspace in a new browser tab\. It may take a few minutes for the Workspace to open if it's **Idle**\. 
**Note**  
Only one user can open and work in a Workspace at a time\. If you select a Workspace that is already in use, a notification appears at the top of the Studio when you try to open it\.

## Understand the Workspace User Interface<a name="emr-studio-workspace-ui"></a>

The EMR Studio Workspace user interface is based on the [JupyterLab interface](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) with icon\-denoted tabs on the left sidebar\. When you pause over an icon, you can see a tooltip that shows the name of the tab\. Choose tabs from the left sidebar to access the following panels\.
+ **File Browser** – Displays the files and directories in your Workspace, as well as the files and directories of your linked Git repositories\.
+ **Running Kernels and Terminals** – Lists all of the kernels and terminals running in your Workspace\. For more information, see [Managing Kernels and Terminals](https://jupyterlab.readthedocs.io/en/latest/user/running.html) in the official JupyterLab documentation\.
+ **EMR Clusters** – Lets you attach a cluster to or detach a cluster from your Workspace to run code in your notebooks\. The EMR cluster configuration panel also provides advanced configuration options to help you create and attach a *new* cluster to your Workspace\. For more information, see [Create a New EMR Cluster to Attach to Your Workspace](emr-studio-create-use-clusters.md#emr-studio-create-cluster)\.
+ **Git** – Provides a graphical user interface for performing commands in the Git repositories attached to your Workspace\. This panel is a JupyterLab extension called jupyterlab\-git\. For more information, see [jupyterlab\-git](https://github.com/jupyterlab/jupyterlab-git)\.
+ **EMR Git Repository** – Helps you link your Workspace with up to three Git repositories\. For details and instructions, see [Link Git\-Based Repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.
+ **Commands** – Offers a keyboard\-driven way to search for and run JupyterLab commands\. For more information, see the [Command Palette](https://jupyterlab.readthedocs.io/en/latest/user/commands.html) page in the JupyterLab documentation\.
+ **Notebook Tools** – Lets you select and set options such as cell slide type and metadata\. The **Notebook Tools** option only appears in the left sidebar after you open a notebook file\.
+ **Open Tabs** – Lists the open documents and activities in the main work area so that you can easily jump to an open tab\. For more information, see the [Tabs and Single\-Document Mode](https://jupyterlab.readthedocs.io/en/latest/user/interface.html#tabs-and-single-document-mode) page in the JupyterLab documentation\.

## Save Workspace Content<a name="emr-studio-save-workspace"></a>

When you work in the notebook editor of a Workspace, EMR Studio automatically saves the content of your notebook cells and output in the Amazon S3 location associated with the Workspace\. This backup process preserves your work between sessions\. 

You can also manually save a notebook by pressing **CTRL\+S** in the open notebook tab or by using one of the save options under **File**\.

Another way to back up the notebook files in your Workspace is to associate your Workspace with a Git\-based repository and sync your changes with the remote repository\. Doing so also lets you save and share your notebooks with team members who are using a different Workspace or Studio\. For instructions, see [Link Git\-Based Repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.

## Delete a Workspace and Notebook Files<a name="emr-studio-delete-workspace"></a>

When you delete a notebook file from an EMR Studio Workspace, you delete the file from the** File browser**, and EMR Studio removes its backup copy in Amazon S3 You do not have to take any further steps to avoid storage charges when you delete a file from a Workspace\.

When you delete *an entire Workspace*, EMR Studio does not remove any corresponding notebook files and folders in Amazon S3\. These files remain in Amazon S3 and continue to accrue storage charges\. To avoid storage charges, you must remove all backed\-up files and folders associated with a deleted Workspace from Amazon S3\.

**To delete a notebook file from an EMR Studio Workspace**

1. Select the **File browser** panel from the left sidebar in your Workspace\.

1. Select the file or folder you want to delete\. Right\-click your selection and choose **Delete**\. The file disappears from the list and can no longer be opened\. EMR Studio removes the file or folder from Amazon S3 for you\.

**To delete a Workspace and its associated backup files from EMR Studio**

1. Log in to your EMR Studio with your Studio access URL and choose **Workspaces** from the left navigation\.

1. Find your Workspace in the list, then select the check box next to its name\. You can select multiple Workspaces to delete at the same time\.

1. Choose **Delete** in the upper right of the **Workspaces** list and confirm that you want to delete the selected Workspaces\. Choose **Delete** to confirm\.

1. Follow the instructions for [Deleting objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-objects.html) in the *Amazon Simple Storage Service* *Console User Guide* to remove notebook files associated with the deleted Workspace from Amazon S3\. If you did not create the Studio, consult your Studio administrator to determine the Amazon S3 backup location for the deleted Workspace\.