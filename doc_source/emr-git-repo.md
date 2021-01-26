# Associating Git\-based Repositories with EMR Notebooks<a name="emr-git-repo"></a>

You can associate Git\-based repositories with your Amazon EMR notebooks to save your notebooks in a version controlled environment\. You can associate up to three repositories with a notebook\. The following Git\-based services are supported:
+ [AWS CodeCommit](http://aws.amazon.com/codecommit)
+ [GitHub](https://www.github.com)
+ [Bitbucket](https://bitbucket.org/)

Associating Git\-based repositories with your notebook has the following benefits\.
+ **Version control** – You can record code changes in a version\-control system so that you can review the history of your changes and selectively reverse them\.
+ **Collaboration** – Colleagues working in diﬀerent notebooks can share code through remote Git\-based repositories\. Notebooks can clone or merge code from remote repositories and push changes back to those remote repositories\.
+ **Code reuse** – Many Jupyter notebooks that demonstrate data analysis or machine learning techniques are available in publicly hosted repositories, such as GitHub\. You can associate your notebooks with a repository to reuse the Jupyter notebooks contained in a repository\.

To use Git\-based repositories with EMR Notebooks, you add the repositories as resources in the Amazon EMR console, associate credentials for repositories that require authentication, and link them with your notebooks\. You can view a list of repositories that are stored in your account and details about each repository in the Amazon EMR console\. You can associate an existing Git\-based repository with a notebook when you create it\. 

**Topics**
+ [Prerequisites and Considerations](emr-managed-notebooks-git-considerations.md)
+ [Add a Git\-based Repository to Amazon EMR](emr-git-repo-add.md)
+ [Update or Delete a Git\-based Repository](emr-git-repo-delete.md)
+ [Link or Unlink a Git\-based Repository](emr-git-repo-link.md)
+ [Create a New Notebook with an Associated Git Repository](emr-git-repo-create-notebook.md)
+ [Use Git Repositories in a Notebook](emr-git-repo-open.md)