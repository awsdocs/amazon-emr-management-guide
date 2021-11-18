# Workspaces in Amazon EMR Studio<a name="emr-studio-configure-workspace"></a>

When you use an Amazon EMR Studio, you can create and configure different *Workspaces* to organize and run notebooks\. This section covers creating and working with Workspaces\. For a conceptual overview, see [Workspaces](how-emr-studio-works.md#emr-studio-workspaces) on the [How Amazon EMR Studio works](how-emr-studio-works.md) page\.

**Topics**
+ [Understand Workspace status](#emr-studio-workspace-status)
+ [Create an EMR Studio Workspace](#emr-studio-create-workspace)
+ [Launch a Workspace](#emr-studio-use-workspace)
+ [Resolve Workspace connectivity issues](#emr-studio-workspace-stop-start)
+ [Understand the Workspace user interface](#emr-studio-workspace-ui)
+ [Explore notebook examples](#emr-studio-notebook-examples)
+ [Save Workspace content](#emr-studio-save-workspace)
+ [Delete a Workspace and notebook files](#emr-studio-delete-workspace)

## Understand Workspace status<a name="emr-studio-workspace-status"></a>

After you create an EMR Studio Workspace, it appears as a row in the **Workspaces** list in your Studio with its name, status, creation time, and last modified timestamp\. The following table describes Workspace statuses\.


****  

| Status | Description | 
| --- | --- | 
| Starting | The Workspace is being prepared, but is not yet ready to use\. You can't open a Workspace when its status is Starting\. | 
| Ready | You can open the Workspace to use the notebook editor, but you must attach the Workspace to an EMR cluster before you can run notebook code\. | 
| Attaching | The Workspace is being attached to a cluster\. | 
| Attached | The Workspace is attached to an EMR cluster and ready for you to write and run notebook code\. If a Workspace's status is not Attached, you must attach it to a cluster before you can run notebook code\. | 
| Idle | The Workspace is stopped and idle\. To reactivate an idle Workspace, select it from the Workspaces list\. The status changes from Idle to Starting to Ready when you select the Workspace\. | 
| Stopping | The Workspace is being stopped and will be set to Idle\. EMR Studio stops notebooks that have been inactive for a long time\. | 
| Deleting | When you delete a Workspace, EMR Studio marks it for deletion and starts the deletion process\. After the deletion process completes, the Workspace disappears from the list\. | 

## Create an EMR Studio Workspace<a name="emr-studio-create-workspace"></a>

You can create EMR Studio Workspaces to run notebook code using the EMR Studio interface\. 

**To create a Workspace in an EMR Studio**

1. Log in to your EMR Studio\.

1. Launch the **Create a Workspace** dialog box\. You can start the Workspace creation dialog box in an EMR Studio in three ways\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-configure-workspace.html)

1. Enter a **Workspace name** and a **Description**\. Naming a Workspace helps you identify it on the **Workspaces** page\.

1. \(Optional\) To attach a cluster to a Workspace when you create the Workspace, expand the **Advanced configuration** section\. 
**Note**  
Provisioning a new cluster requires access permissions from your administrator\. 

   Choose one of the cluster options for the Workspace and attach the cluster\. For more information about provisioning a cluster when you create a Workspace, see [Create a new EMR Cluster for a Workspace](emr-studio-create-use-clusters.md#emr-studio-create-cluster)\.

1. Choose **Create Workspace** in the lower right of the page\. 

After you create a Workspace, EMR Studio will open the **Workspaces** page\. You will see a green success banner at the top of the page and can find the newly\-created Workspace in the list\.

## Launch a Workspace<a name="emr-studio-use-workspace"></a>

To start working with notebook files, launch a Workspace to access the notebook editor\. The Workspace page of a Studio lists all of the Workspaces created in that Studio with details including **Name**, **Status**, **Creation time**, and **Last modified**\. 

**To launch a Workspace for editing and running notebooks**

1. On the **Workspaces** page of your Studio, find the Workspace\. You can filter the list by keyword or by column value\.

1. Choose the Workspace name to launch the Workspace in a new browser tab\. It may take a few minutes for the Workspace to open if it's **Idle**\. 
**Note**  
Only one user can open and work in a Workspace at a time\. If you select a Workspace that is already in use, EMR Studio displays a notification when you try to open it\. The **User** column on the **Workspaces** page shows the user working in the Workspace\.

## Resolve Workspace connectivity issues<a name="emr-studio-workspace-stop-start"></a>

To resolve Workspace connectivity issues, you can stop and restart a Workspace\. When you restart a Workspace, EMR Studio launches the Workspace in a different Availability Zone or a different subnet that is associated with your Studio\.

**To stop and restart an EMR Studio Workspace**

1. Close the Workspace in your browser\.

1. Navigate to the **Workspace** list in the EMR Studio UI\.

1. Select your Workspace from the list and choose **Actions**\.

1. Choose **Stop** and wait for the Workspace status to change from **Stopping** to **Idle**\.

1. Choose **Actions** again, and then choose **Start** to restart the Workspace\.

1. Wait for the Workspace status to change from **Starting** to **Ready**, then choose the Workspace name to reopen it in a new browser tab\.

## Understand the Workspace user interface<a name="emr-studio-workspace-ui"></a>

The EMR Studio Workspace user interface is based on the [JupyterLab interface](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) with icon\-denoted tabs on the left sidebar\. When you pause over an icon, you can see a tooltip that shows the name of the tab\. Choose tabs from the left sidebar to access the following panels\.
+ **File Browser** – Displays the files and directories in the Workspace, as well as the files and directories of linked Git repositories\.
+ **Running Kernels and Terminals** – Lists all of the kernels and terminals running in the Workspace\. For more information, see [Managing kernels and terminals](https://jupyterlab.readthedocs.io/en/latest/user/running.html) in the official JupyterLab documentation\.
+ **Git** – Provides a graphical user interface for performing commands in the Git repositories attached to the Workspace\. This panel is a JupyterLab extension called jupyterlab\-git\. For more information, see [jupyterlab\-git](https://github.com/jupyterlab/jupyterlab-git)\.
+ **EMR Clusters** – Lets you attach a cluster to or detach a cluster from the Workspace to run notebook code\. The EMR cluster configuration panel also provides advanced configuration options to help you create and attach a *new* cluster to the Workspace\. For more information, see [Create a new EMR Cluster for a Workspace](emr-studio-create-use-clusters.md#emr-studio-create-cluster)\.
+ **EMR Git Repository** – Helps you link the Workspace with up to three Git repositories\. For details and instructions, see [Link Git\-based repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.
+ **Notebook Examples** – Provides a list of notebook examples that you can save to the Workspace\. You can also access the examples by choosing **Notebook Examples** on the **Launcher** page of the Workspace\. 
+ **Commands** – Offers a keyboard\-driven way to search for and run JupyterLab commands\. For more information, see the [Command palette](https://jupyterlab.readthedocs.io/en/latest/user/commands.html) page in the JupyterLab documentation\.
+ **Notebook Tools** – Lets you select and set options such as cell slide type and metadata\. The **Notebook Tools** option appears in the left sidebar after you open a notebook file\.
+ **Open Tabs** – Lists the open documents and activities in the main work area so that you can jump to an open tab\. For more information, see the [Tabs and single\-document mode](https://jupyterlab.readthedocs.io/en/latest/user/interface.html#tabs-and-single-document-mode) page in the JupyterLab documentation\.

## Explore notebook examples<a name="emr-studio-notebook-examples"></a>

Every EMR Studio Workspace includes a set of notebook examples that you can use to explore EMR Studio features\. To edit or run a notebook example, you can save it to the Workspace\.

**To save a notebook example to a Workspace**

1. From the left sidebar, choose the **Notebook Examples** tab to open the **Notebook Examples** panel\. You can also access the examples by choosing **Notebook Examples** on the **Launcher** page of the Workspace\. 

1. Choose a notebook example to preview it in the main work area\. The example is read\-only\.

1. To save the notebook example to the Workspace, choose **Save to Workspace**\. EMR Studio saves the example in your home directory\. After you save a notebook example to the Workspace, you can rename, edit, and run it\.

For more information about the notebook examples, see the [EMR Studio Notebook examples GitHub repository](https://github.com/aws-samples/emr-studio-notebook-examples)\.

## Save Workspace content<a name="emr-studio-save-workspace"></a>

When you work in the notebook editor of a Workspace, EMR Studio saves the content of notebook cells and output for you in the Amazon S3 location associated with the Studio\. This backup process preserves work between sessions\. 

You can also save a notebook by pressing **CTRL\+S** in the open notebook tab or by using one of the save options under **File**\.

Another way to back up the notebook files in a Workspace is to associate the Workspace with a Git\-based repository and sync your changes with the remote repository\. Doing so also lets you save and share notebooks with team members who use a different Workspace or Studio\. For instructions, see [Link Git\-based repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.

## Delete a Workspace and notebook files<a name="emr-studio-delete-workspace"></a>

When you delete a notebook file from an EMR Studio Workspace, you delete the file from the** File browser**, and EMR Studio removes its backup copy in Amazon S3\. You do not have to take any further steps to avoid storage charges when you delete a file from a Workspace\.

When you delete *an entire Workspace*, EMR Studio does not remove any corresponding notebook files and folders in Amazon S3\. These files remain in Amazon S3 and continue to accrue storage charges\. To avoid storage charges, you must remove all backed\-up files and folders associated with a deleted Workspace from Amazon S3\.

**To delete a notebook file from an EMR Studio Workspace**

1. Select the **File browser** panel from the left sidebar in the Workspace\.

1. Select the file or folder you want to delete\. Right\-click your selection and choose **Delete**\. The file disappears from the list and can no longer be opened\. EMR Studio removes the file or folder from Amazon S3 for you\.

**To delete a Workspace and its associated backup files from EMR Studio**

1. Log in to your EMR Studio with your Studio access URL and choose **Workspaces** from the left navigation\.

1. Find your Workspace in the list, then select the check box next to its name\. You can select multiple Workspaces to delete at the same time\.

1. Choose **Delete** in the upper right of the **Workspaces** list and confirm that you want to delete the selected Workspaces\. Choose **Delete** to confirm\.

1. Follow the instructions for [Deleting objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-objects.html) in the *Amazon Simple Storage Service* *Console User Guide* to remove the notebook files associated with the deleted Workspace from Amazon S3\. If you did not create the Studio, consult your Studio administrator to determine the Amazon S3 backup location for the deleted Workspace\.