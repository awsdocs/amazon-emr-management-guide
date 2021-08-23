# Link Git\-based repositories to an EMR Studio Workspace<a name="emr-studio-git-repo"></a>

## About Git repositories for EMR Studio<a name="emr-studio-git-repo-about"></a>

You can associate a maximum of three Git repositories with an EMR Studio Workspace\. By default, each Workspace lets you choose from a list of Git repositories that are associated with the same AWS account as your Studio\. You can also create a new Git repository as a resource for your Workspace\.

You can manually run Git commands like the following using a terminal command while connected to the master node of a cluster\. 

```
!git pull origin <branch-name>
```

Alternatively, you can use he jupyterlab\-git extension, which is installed and available to use in each Workspace\. Open it from the left sidebar by choosing the **Git** icon\. For information about the jupyterlab\-git extension for JupyterLab, see [jupyterlab\-git](https://github.com/jupyterlab/jupyterlab-git)\.

## Prerequisites<a name="emr-studio-git-prereqs"></a>
+ To associate a Git repository with your Workspace, your Studio must be configured to allow Git repository linking\. Your Studio administrator should take steps to [Establish access and permissions for Git\-based repositories](emr-studio-enable-git.md)\.
+ If you use a CodeCommit repository, you must use Git credentials and HTTPS\. SSH keys and HTTPS with the AWS Command Line Interface credential helper are not supported\. CodeCommit also does not support personal access tokens \(PATs\)\. For more information, see [Using IAM with CodeCommit](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html) in the *IAM User Guide* and [Setup for HTTPS users using Git credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) in the *AWS CodeCommit User Guide*\.

## Instructions<a name="emr-studio-link-git-repo"></a>

**To link an associated Git repository to your Workspace**

1. Open the Workspace that you want to link to a repository from the **Workspaces** list in your Studio\.

1. In the left sidebar, choose the **EMR Git Repository** icon to open the **Git repository** tool panel\.

1. Under **Git repositories**, expand the dropdown list and select a maximum of three different repositories to link to your Workspace\. EMR Studio will automatically register your selection and begin linking each repository\. 

It might take some time for the linking process to complete\. You can see the status for each repository that you selected in the **Git repository** tool panel\. After EMR Studio links a repository to your Workspace, you should see the files that belong to that repository appear in the **File browser** panel\.

**To add a new Git repository to your Workspace as a resource**

1. Open the Workspace that you want to link to a repository from the Workspaces list in your Studio\.

1. In the left sidebar, choose the **EMR Git Repository** icon to open the **Git repository** tool panel\.

1. Choose **Add new Git repository**\.

1. For **Repository name**, enter a descriptive name for the repository in EMR Studio\. Names may only contain alphanumeric characters, hyphens, and underscores\.

1. For **Git repository URL**, enter the URL for the repository\. When you use a CodeCommit repository, this is the URL that is copied when you choose **Clone URL** and then **Clone HTTPS**\. For example, `https://git-codecommit.us-west-2.amazonaws.com/v1/repos/[MyCodeCommitRepoName]`\.

1. For **Branch**, enter the name of an existing branch that you want to check out\.

1. For Git credentials, choose an option according to the following guidelines\. EMR Studio accesses your Git credentials using secrets stored in Secrets Manager\.
**Note**  
If you use a GitHub repository, we recommend that you use a personal access token \(PAT\) to authenticate\. Beginning August 13, 2021, GitHub will require token\-based authentication and will no longer accept passwords when authenticating Git operations\. For more information, see the [Token authentication requirements for Git operations](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) post in *The GitHub Blog*\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-git-repo.html)

1. Choose **Add repository** to create the new repository\. After EMR Studio successfully creates the new repository, you will see a success message\. The new repository appears in the dropdown list under **Git repositories**\.

1. To link your new repository to your Workspace, choose it from the dropdown list under **Git repositories**\.

It might take some time for the linking process to complete\. After EMR Studio links your new repository to your Workspace, you should see a new folder with the same name as your repository appear in the **File Browser** panel\.

To open a different linked repository, navigate to its folder in the **File browser**\. 