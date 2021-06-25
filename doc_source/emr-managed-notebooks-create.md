# Creating a Notebook<a name="emr-managed-notebooks-create"></a>

You create an EMR notebook using the Amazon EMR console\. Creating notebooks using the AWS CLI or the Amazon EMR API is not supported\.

**To create an EMR notebook**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Notebooks**, **Create notebook**\.

1. Enter a **Notebook name** and an optional **Notebook description**\.

1. If you have an active cluster to which you want to attach the notebook, leave the default **Choose an existing cluster** selected, click **Choose**, select a cluster from the list, and then click **Choose cluster**\. For information about cluster requirements for EMR Notebooks, see [Considerations when using EMR Notebooks](emr-managed-notebooks-considerations.md)\.

   **—or—**

   Choose **Create a cluster**, enter a **Cluster name** and choose options according to the following guidelines\. The cluster is created in the default VPC for the account using On\-Demand instances\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-create.html)

1. For **Security groups**, choose **Use default security groups**\. Alternatively, choose **Choose security groups** and select custom security groups that are available in the VPC of the cluster\. You select one for the master instance and another for the notebook client instance\. For more information, see [Specifying EC2 security groups for EMR Notebooks](emr-managed-notebooks-security-groups.md)\.

1. For **AWS Service Role**, leave the default or choose a custom role from the list\. The client instance for the notebook uses this role\. For more information, see [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md)\.

1. For **Notebook location** choose the location in Amazon S3 where the notebook file is saved, or specify your own location\. If the bucket and folder don't exist, Amazon EMR creates it\.

   Amazon EMR creates a folder with the **Notebook ID** as folder name, and saves the notebook to a file named `NotebookName.ipynb`\. For example, if you specify the Amazon S3 location `s3://MyBucket/MyNotebooks` for a notebook named `MyFirstEMRManagedNotebook`, the notebook file is saved to `s3://MyBucket/MyNotebooks/NotebookID/MyFirstEMRManagedNotebook.ipynb`\.

   If you specify an encrypted location in Amazon S3, you must set up the [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md) as a key user\. The default service role is `EMR_Notebooks_DefaultRole`\. If you are using an AWS KMS key for encryption, see [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-users-crypto) in the AWS Key Management Service Developer Guide and the [support article for adding key users](https://aws.amazon.com/premiumsupport/knowledge-center/s3-bucket-access-default-encryption/)\.

1. Optionally, if you have added a Git\-based repository to Amazon EMR that you want to associate with this notebook, choose **Git repository**, select **Choose repository** and then select a repository from the list\. For more information, see [Associating Git\-based repositories with EMR Notebooks](emr-git-repo.md)\.

1. Optionally, choose **Tags**, and then add any additional key\-value tags for the notebook\.
**Important**  
A default tag with the **Key** string set to `creatorUserID` and the value set to your IAM user ID is applied for access purposes\. We recommend that you do not change or remove this tag because it can be used to control access\. For more information, see [Use cluster and Notebook tags with IAM policies for access control](security_iam_emr-with-iam.md#emr-tag-based-access)\.

1. Choose **Create Notebook**\.