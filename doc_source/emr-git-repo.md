# Associate Git Repositories with Amazon EMR Notebooks<a name="emr-git-repo"></a>

You can associate Git repositories with your Amazon EMR notebooks to save your notebooks in a version controlled environment\. You can associate up to three repositories with a notebook\. The Git repositories must be hosted in the following web\-based Git hosting services: GitHub or Bitbucket\. Associating Git repositories with your notebook can be useful for:
+ Version control – Storing notebooks in a Git repository enables you to record code changes in a version\-control system so that you can review the history of your changes and selectively reverse some of the changes\.
+ Collaboration – Storing your notebooks in Git repositories allows peers working in diﬀerent notebooks to share code through remote Git repositories\. Notebooks can clone or merge code from remote Git repositories and push changes back to those remote repositories\.
+ Code reuse – Many Jupyter notebooks that demonstrate data analysis or machine learning techniques are available in publicly hosted Git repositories, such as on GitHub\. You can associate your notebooks with a repository to reuse the Jupyter notebooks contained in that repository\.

Before associating a Git repository with your notebook, you need to make sure that your cluster, IAM role, and security groups have the correct settings and permissions\. 
+ The network interface that is launched has only a private IP address\. This means that the cluster that your notebook connects to must be in a private subnet with a network address translation \(NAT\) gateway or must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC Options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-clusters-in-a-vpc.html)\.
**Important**  
If the network interface is launched into a public subnet, it won’t be able to communicate with the internet through an internet gateway \(IGW\)\.
+ If you need a secret for the repository, the service role for EMR notebooks must have the `secretsmanager:GetSecretValue` permission in its IAM policy\. For more information, see [Service Role for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-service-role.html)\.
+ The security groups for your notebook must include an outbound rule to allow the notebook to route traffic to the internet via the cluster\. It is recommended that you create your own security groups\. For more information, see [Specifying EC2 Security Groups for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-security-groups.html)\.

To manage your Git repositories, add the repositories as resources in the Amazon EMR console, associate credentials for repositories that require authentication, and link them with your notebooks\. You can view a list of repositories that are stored in your account and details about each repository in the Amazon EMR console\. You can also create a notebook with an existing Git repository\. 

**Topics**
+ [Add a Git Repository to Amazon EMR](emr-git-repo-add.md)
+ [Update or Delete a Git Repository](emr-git-repo-delete.md)
+ [Link or Unlink a Git Repository](emr-git-repo-link.md)
+ [Create a New Notebook with an Associated Git Repository](emr-git-repo-create-notebook.md)
+ [Use Git Repositories in a Notebook](emr-git-repo-open.md)