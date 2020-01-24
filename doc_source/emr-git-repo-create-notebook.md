# Create a New Notebook with an Associated Git Repository<a name="emr-git-repo-create-notebook"></a>

**To create a notebook and associate it with Git repositories in the AWS Management Console**

1. Follow the instructions at [Creating a Notebook](emr-managed-notebooks-create.md)\.

1. For **Security group**, choose **Use your own security group**\.
**Note**  
The security groups for your notebook must include an outbound rule to allow the notebook to route traffic to the internet via the cluster\. It is recommended that you create your own security groups\. For more information, see [Specifying EC2 Security Groups for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-security-groups.html)\.

1. For **Git repositories**, **Choose repository** to associate with the notebook\.

   1. Choose a repository that is stored as a resource in your account, and then choose **Save**\.

   1. To add a new repository as a resource in your account, choose **add a new repository**\. Complete the **Add repository** workflow in a new window\. 