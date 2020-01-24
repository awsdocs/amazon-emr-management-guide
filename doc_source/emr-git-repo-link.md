# Link or Unlink a Git Repository<a name="emr-git-repo-link"></a>

**To link a Git repository to an EMR notebook**

The repository can be linked to a notebook once the notebook is **Ready**\. 

1. From the **Notebooks** list, choose the notebook you want to update\. 

1. In the **Git repositories** section on the **Notebook** page, choose **Link new repository**\.

1. In the repository list of the **Link Git repository to notebook** window, select one or more repositories that you want to link to your notebook, and then choose **Link repository**\.

Or

1. On the **Git repositories** page, choose the repository you want to link to your notebook\. 

1. In the list of **EMR notebooks**, choose **Link new notebook** to link this repository to an existing notebook\. 

**To unlink a Git repository from an EMR notebook**

1. From the **Notebooks** list, choose the notebook you want to update\. 

1. In the list of **Git repositories**, select the repository that you want to unlink from your notebook, and then choose **Unlink repository**\.

Or

1. On the **Git repositories** page, choose the repository you want to make updates to\. 

1. In the list of **EMR notebooks**, select the notebook that you want to unlink from the repository, and then choose **Unlink notebook**\.

**Note**  
Linking a Git repository to a notebook clones the remote repository to your local Jupyter notebook\. Unlinking the Git repository from a notebook only disconnects the notebook from the remote repository but does not delete the local Git repository\. 

## Understanding Repository Status<a name="emr-managed-notebooks-repository-status"></a>

A Git repository may have any of the following status in the repository list\. For more information about linking EMR notebooks with Git repositories, see [Link or Unlink a Git Repository](#emr-git-repo-link)\.


| Status | Meaning | 
| --- | --- | 
|  Linking  |  The Git repository is being linked to the notebook\. While the repository is **Linking**, you cannot stop the notebook\.  | 
|  Linked  |  The Git repository is linked to the notebook\. While the repository has a **Linked** status, it is connected to the remote repository\.  | 
|  Link Failed  |  The Git repository failed to link to the notebook\. You can try again to link it\.  | 
|  Unlinking  |  The Git repository is being unlinked from the notebook\. While the repository is **Unlinking**, you cannot stop the notebook\. Unlinking a Git repository from a notebook only disconnects it from the remote repository; it doesn't delete any code from the notebook\.  | 
|  Unlink Failed  |  The Git repository failed to unlink from the notebook\. You can try again to unlink it\.  | 