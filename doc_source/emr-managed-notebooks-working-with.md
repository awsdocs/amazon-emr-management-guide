# Working with Notebooks<a name="emr-managed-notebooks-working-with"></a>

After you create an EMR notebook, the notebook takes a short time to start\. The **Status** in the **Notebooks** list shows **Starting**\. You can open a notebook when its status is **Ready**\. It might take a bit longer for a notebook to be **Ready** if you created a cluster along with it\.

**Tip**  
Refresh your browser or choose the refresh icon above the notebooks list to refresh notebook status\.

## Understanding Notebook Status<a name="emr-managed-notebooks-status"></a>

An EMR notebook can have the following for **Status** in the **Notebooks** list\.


| Status | Meaning | 
| --- | --- | 
|  Ready  |  You can open the notebook using the notebook editor\. While a notebook has a **Ready** status, you can stop or delete it\. To change clusters, you must stop the notebook first\. If a notebook in the **Ready** status is idle for a long period of time, it is stopped automatically\.  | 
|  Starting  |  The notebook is being created and attached to the cluster\. While a notebook is starting, you cannot open the notebook editor, stop it, delete it, or change clusters\.  | 
|  Pending  |  The notebook has been created, and is waiting for integration with the cluster to complete\. The cluster may still be provisioning resources or responding to other requests\. You can open the notebook editor with the notebook in *local mode*\. Any code that relies on cluster processes does not execute and fails\.  | 
|  Stopping  |  The notebook is shutting down, or the cluster that the notebook is attached to is terminating\. While a notebook is stopping, you can't open the notebook editor, stop it, delete it, or change clusters\.  | 
|  Stopped  |  The notebook has shut down\. You can start the notebook on the same cluster, as long as the cluster is still running\. You can change clusters, and delete the cluster\.  | 
|  Deleting  |  The cluster is being removed from the list of available clusters\. The notebook file, `NotebookName.ipynb `remains in Amazon S3 and continues to accrue applicable storage charges\.  | 

## Working with the Notebook Editor<a name="emr-managed-notebooks-editor"></a>

An advantage of using an EMR notebook is that you can launch the notebook in Jupyter or JupyterLab directly from the console\.

With EMR Notebooks, the notebook editor you access from the Amazon EMR console is the familiar open\-source Jupyter Notebook editor or JupyterLab\. Because the notebook editor is launched within the Amazon EMR console, it's more efficient to configure access than it is with a notebook hosted on an Amazon EMR cluster\. You don't need to configure a user's client to have web access through SSH, security group rules, and proxy configurations\. If a user has sufficient permissions, they can simply open the notebook editor within the Amazon EMR console\.

Only one user can have an EMR notebook open at a time from within Amazon EMR\. If another user tries to open an EMR notebook that is already open, an error occurs\.

**Important**  
Amazon EMR creates a unique pre\-signed URL for each notebook editor session, which is valid only for a short time\. We recommend that you do not share the notebook editor URL\. Doing this creates a security risk because recipients of the URL adopt your permissions to edit the notebook and run notebook code for the lifetime of the URL\. If others need access to a notebook, provide permissions to their IAM user through permissions policies\. For more information, see [EMR Notebooks Security and Access Control](emr-managed-notebooks-security.md)\.

**To open the notebook editor for an EMR notebook**

1. Select a notebook with a **Status** of **Ready** or **Pending** from the **Notebooks** list\.

1. Choose **Open in JupyterLab** or **Open in Jupyter**\.

   A new browser tab opens to the JupyterLab or Jupyter Notebook editor\.

1. From the **Kernel** menu, choose **Change kernel** and then select the kernel for your programming language\.

   You are now ready to write and run code from within the notebook editor\.

### Saving the Contents of a Notebook<a name="emr-managed-notebooks-saving"></a>

When you work in the notebook editor, the contents of notebook cells and output are saved automatically to the notebook file periodically in Amazon S3\. A notebook that has no changes since the last time a cell was edited shows **\(autosaved\)** next to the notebook name in the editor\. If changes have not yet been saved, **unsaved changes** appears\.

You can save a notebook manually\. From the **File** menu, choose **Save and Checkpoint** or press CTRL\+S\. This creates a file named `NotebookName.ipynb` in a **checkpoints** folder within the notebook folder in Amazon S3\. For example, `s3://MyBucket/MyNotebookFolder/NotebookID/checkpoints/NotebookName.ipynb`\. Only the most recent checkpoint file is saved in this location\.

## Changing Clusters<a name="emr-managed-notebooks-changing-clusters"></a>

You can change the cluster that an EMR notebook is attached to without changing the contents of the notebook itself\. You can change clusters for only those notebooks that have a **Stopped** status\.

**To change the cluster of an EMR notebook**

1. If the notebook that you want to change is running, select it from the **Notebooks** list and choose **Stop**\.

1. When the notebook status is **Stopped**, select the notebook from the **Notebooks** list, and then choose **View details**\.

1. Choose **Change cluster**\.

1. If you have an active cluster running Hadoop, Spark, and Livy to which you want to attach the notebook, leave the default, and select a cluster from the list\. Only clusters that meet the requirements are listed\.

   —or—

   Choose **Create a cluster** and then choose the cluster options\. For more information, see [Creating a Cluster When You Create a Notebook](emr-managed-notebooks-cluster.md#emr-managed-notebooks-new-cluster)\.

1. Choose an option for **Security groups**, and then choose **Change cluster and start notebook**\.

## Deleting Notebooks and Notebook Files<a name="emr-managed-notebooks-deleting"></a>

When you delete an EMR notebook using the Amazon EMR console, you delete the notebook from the list of available notebooks\. However, notebook files remain in Amazon S3 and continue to accrue storage charges\.

**To delete a notebook and remove associated files**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Notebooks**, select your notebook from the list, and then choose **View details**\.

1. Choose the folder icon next to **Notebook location** and copy the **URL**, which is in the pattern `s3://MyNotebookLocationPath/NotebookID/`\.

1. Choose **Delete**\.

   The notebook is removed from the list, and notebook details can no longer be viewed\.

1. Follow the instructions for [How do I Delete Folders from an S3 Bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-folders.html) in the Amazon Simple Storage Service Console User Guide\. Navigate to the bucket and folder from step 3\.

   —or—

   If you have the AWS CLI installed, open a command prompt and type the command at the end of this paragraph\. Replace the Amazon S3 location with the location that you copied above\. Make sure that the AWS CLI is configured with the access keys of a user with permissions to delete the Amazon S3 location\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/cli-chap-getting-started.html) in the *AWS Command Line Interface User Guide*\.

   ```
   aws s3 rm s3://MyNotebookLocationPath/NotebookID
   ```

## Sharing Notebook Files<a name="emr-managed-notebooks-file-sharing"></a>

Each EMR notebook is saved to Amazon S3 as a file named `NotebookName.ipynb`\. As long as a notebook file is compatible with the same version of Jupyter Notebook that EMR Notebooks is based on, you can open the notebook as an EMR notebook\. You can replace the file for an EMR notebook with a different notebook file of the same name\.

You can use this process to use EMR notebooks shared by others, notebooks shared in the Jupyter community, or to restore a notebook that was deleted from the console when you still have the notebook file\.

**To use a different notebook file as the basis for an EMR notebook**

1. Before proceeding, close the notebook editor for any notebooks that you will work with, and then stop the notebook if it's an EMR notebook\.

1. Create an EMR notebook and enter a name for it\. The name that you enter for the notebook will be the name of the file you need to replace\. The new file name must match this file name exactly\.

1. Make a note of the location in Amazon S3 that you choose for the notebook\. The file that you replace is in a folder with a path and file name like the following pattern: `s3://MyNotebookLocation/NotebookID/MyNotebookName.ipynb`\.

1. Stop the notebook\.

1. Replace the old notebook file in the Amazon S3 location with the new one, using exactly the same name\.

   The following AWS CLI command for Amazon S3 replaces a file saved to a local machine called `SharedNotebook.ipynb` for an EMR notebook with the name **MyNotebook**, an ID of `e-12A3BCDEFJHIJKLMNO45PQRST`, and created with `MyBucket/MyNotebooksFolder` specified in Amazon S3\. For information about using the Amazon S3 console to copy and replace files, see [Uploading, Downloading, and Managing Objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-download-objects.html) in the *Amazon Simple Storage Service Console User Guide*\.

   ```
   aws s3 cp SharedNotebook.ipynb s3://MyBucket/MyNotebooksFolder/-12A3BCDEFJHIJKLMNO45PQRST/MyNotebook.ipynb
   ```