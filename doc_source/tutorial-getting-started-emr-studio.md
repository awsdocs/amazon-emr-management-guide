# Tutorial: Getting Started with the Amazon EMR Studio Interface<a name="tutorial-getting-started-emr-studio"></a>

Get started with Amazon EMR Studio to run notebook queries and code\. This tutorial shows you how to log in to an EMR Studio, create a [Workspace](how-emr-studio-works.md#emr-studio-workspaces), connect your Workspace to an EMR cluster, and start working in a notebook file\. 

**Topics**
+ [Step 1: Log In to Your EMR Studio](#emr-studio-getting-started-log-in)
+ [Step 2: Create a Workspace](#emr-studio-getting-started-create-workspace)
+ [Step 3: Open your Workspace](#emr-studio-getting-started-open-workspace)
+ [Step 4: Attach a Cluster and Start a Notebook File](#emr-studio-getting-started-explore-notebook)
+ [Next Steps](#emr-studio-getting-started-next-steps)

## Step 1: Log In to Your EMR Studio<a name="emr-studio-getting-started-log-in"></a>

After you receive a unique sign\-on \(Studio access\) URL for your Amazon EMR Studio, you can log in to the Studio directly using your corporate credentials\.

**To log in to your EMR Studio**

1. Open your EMR Studio access URL in your browser of choice\. You will see a page with a login prompt\.

1. Enter your corporate user name and password and continue through the login process\.

When logged in to your Studio, you will see the EMR Studio Dashboard page with an overview of the tasks that you can perform in your Studio\.

## Step 2: Create a Workspace<a name="emr-studio-getting-started-create-workspace"></a>

After you log in to your Studio, you can create a *Workspace*\. Workspaces help you organize and run your notebooks\. In this tutorial, you will create a Workspace without an EMR cluster attached to it, and then you will attach a cluster\. For more information, see [Workspaces in Amazon EMR Studio](how-emr-studio-works.md#emr-studio-workspaces) and [Configure a Workspace for EMR Studio](emr-studio-configure-workspace.md)\. 

**To create an EMR Studio Workspace**

1. On the Dashboard page of your Studio, choose **Create Workspace** under the **Use fully\-managed Jupyter Notebooks** section\.

1. In the **Create a Workspace** dialog box, enter a **Workspace name** and a **Description**\. Naming your Workspace helps you identify it when you view the **Workspaces** page in your Studio\.

1. Select a subnet from the **Subnet** dropdown list\. Each subnet in the dropdown list belongs to the same Amazon Virtual Private Cloud \(VPC\) as your Studio\. The subnet determines which EMR clusters you can attach to your Workspace\.

1. Under **S3 location**, choose **Browse S3** to open the bucket selection dialog box\. Search for an S3 bucket by name, select it, and then select **Choose**\. EMR Studio backs up and saves your Workspace and its associated notebook files in this location\. For more information, see [Notebook Storage in Amazon EMR Studio](how-emr-studio-works.md#emr-studio-storage)\.

1. Skip the **Advanced configuration** section\. Advanced configuration lets you create or attach an EMR cluster to a Workspace when you create the Workspace\. Attaching a cluster when you create a Workspace is optional\. For more information, see [Attach a Cluster to Your Workspace](emr-studio-create-use-clusters.md)\.

1. In the lower right of the screen, choose **Create Workspace**\.

After you create your Workspace, your EMR Studio will open the **Workspaces** page\. You will see a green success banner at the top of your page and can find your newly\-created Workspace in the list\. The status of your Workspace changes from **Starting** to **Ready** when it's ready for you to use\.

## Step 3: Open your Workspace<a name="emr-studio-getting-started-open-workspace"></a>

To start working with notebook files, launch your Workspace from the **Workspaces** page of your Studio\. The **Workspaces** page lists all of the Workspaces created in your Studio with details such as **Name**, **Status**, **Creation time**, and **Last modified**\. 

**To open your Workspace for managing and running notebooks**

1. On the **Workspaces** page of your Studio, find your Workspace\. You can filter the list by keyword or by column value\.

1. Choose your Workspace name to launch it in a new browser tab\. It may take a few minutes for the Workspace to open if it's **Idle**\. 
**Note**  
Only one user can open and work in a Workspace at a time\. If you select a Workspace that is already in use, a notification appears at the top of the Workspaces page\.

## Step 4: Attach a Cluster and Start a Notebook File<a name="emr-studio-getting-started-explore-notebook"></a>

After your Workspace opens in a new browser tab, you can create or run notebook files, link Git repositories, and choose an EMR cluster for running notebooks\. In this step, you will attach a cluster to your Workspace so that you can start writing and running notebook code\.

The EMR Studio Workspace interface is based on the [JupyterLab interface](https://jupyterlab.readthedocs.io/en/latest/user/interface.html) with icon\-denoted tabs on the left sidebar\. Choose icons on the left sidebar to access tool panels such as the file browser or JupyterLab command palette\. To learn more about the EMR Studio Workspace interface, see [Understand the Workspace User Interface](emr-studio-configure-workspace.md#emr-studio-workspace-ui)\.

**Note**  
Before you can select a kernel for running your notebook, you must attach a cluster to your Workspace\. Otherwise, an error message appears stating that the selected kernel could not be started\.

**To attach a cluster to your Workspace and start a new notebook file**

1. In the left sidebar, choose the **EMR Clusters** tab to open the cluster configuration panel\. 

1. Under **Select cluster**, expand the dropdown list and select a cluster to attach it to your Workspace\. Choose **Attach**\.

1. In the main work area, select a notebook type from the **Launcher** to open a new notebook file\.

1. In the **Select Kernel** dialog box, choose a kernel to use\. For more information about working with kernels, see [Documents and Kernels](https://jupyterlab.readthedocs.io/en/latest/user/documents_kernels.html) in the JupyterLab documentation\.

1. In the **File browser** panel, find the empty notebook with the same name as your Workspace\. EMR Studio automatically creates this notebook file for you\. You can now work in your notebook and run selected notebook cells\. When you're done and want to exit the Workspace, close its browser tab\. 

1. You can exit the Studio by closing it in your browser\. EMR Studio sessions are valid for 15 minutes after the last session activity, after which you will be logged out\. There's no need to manually log out of a Studio\.

   To avoid additional charges when you're done, make sure you delete your Workspace\. First, close your Workspace\. On the **Workspaces** page of your Studio, find your Workspace and select the check box next to its name\. Choose **Delete**, then choose **Delete** again to confirm\.

## Next Steps<a name="emr-studio-getting-started-next-steps"></a>

Now that you've learned the basics about working with an EMR Studio, here are some additional steps you can take:
+ EMR Studio saves notebook content in Amazon S3, but you can also link up to three Git repositories to a Workspace to work with and share notebook code\. For more information, see [Link Git\-Based Repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.
+ After you run a notebook, you can launch one of the persistent application interfaces in just a few clicks to debug your jobs\. For more information, see [Diagnose Applications and Jobs with EMR Studio](emr-studio-debug.md)\.