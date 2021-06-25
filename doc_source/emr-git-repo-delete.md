# Update or delete a Git\-based repository<a name="emr-git-repo-delete"></a>

**To update a Git\-based repository**

1. On the **Git repositories** page, choose the repository you want to update\.

1. On the repository page, choose **Edit repository**\. 

1. Update **Git credentials** on the repository page\.

**To delete a Git repository**

1. On the **Git repositories** page, choose the repository you want to delete\. 

1. On the repository page, choose all the notebooks that are currently linked to the repository\. Choose **Unlink notebook**\.

1. On the repository page, choose **Delete**\.

**Note**  
To delete the local Git repository from Amazon EMR, you must first unlink any notebooks from this repository\. For more information, see [Link or unlink a Git\-based repository](emr-git-repo-link.md)\. Deleting a Git repository will not delete any secret created for the repository\. You can delete the secret in AWS Secrets Manager\.