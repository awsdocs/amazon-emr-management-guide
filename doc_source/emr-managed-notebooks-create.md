# Creating a Notebook<a name="emr-managed-notebooks-create"></a>

You create an EMR notebook using the Amazon EMR console\. Creating notebooks using the AWS CLI or the Amazon EMR API is not supported\.

**To create an EMR notebook**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Notebooks**, **Create notebook**\.

1. Enter a **Notebook name** and an optional **Notebook description**\.

1. If you have an active cluster running Hadoop, Spark, and Livy to which you want to attach the notebook, leave the default, select **Choose**, select a cluster from the list, and then **Choose cluster**\. Only clusters that meet the requirements are listed\.

   —or—

   Choose **Create a cluster**, enter a **Cluster name** and choose the number and type of EC2 instances for the cluster\. One instance hosts the master node, and the remainder are used for core nodes\. You can also choose a custom service role and EC2 instance profile, if necessary\. For more information, see [Creating a Cluster When You Create a Notebook](emr-managed-notebooks-cluster.md#emr-managed-notebooks-new-cluster)\.

1. For **Security groups**, choose **Use default security groups**\. Alternatively, choose **Choose security groups** and select custom security groups\. You select one for the master instance and another for the notebook service\. For more information, see [Specifying EC2 Security Groups for EMR Notebooks](emr-managed-notebooks-security-groups.md)\.

1. For **AWS Service Role**, leave the default or choose a custom role from the list\. For more information, see [Service Role for EMR Notebooks](emr-managed-notebooks-service-role.md)\.

1. For **Notebook location** choose the location in Amazon S3 where the notebook file is saved, or specify your own location\. If the bucket and folder don't exist, Amazon EMR creates it\.

   Amazon EMR creates a folder with the **Notebook ID** as folder name, and saves the notebook to a file named `NotebookName.ipynb`\. For example, if you specify the Amazon S3 location `s3://MyBucket/MyNotebooks` for a notebook named `MyFirstEMRManagedNotebook`, the notebook file is saved to `s3://MyBucket/MyNotebooks/NotebookID/MyFirstEMRManagedNotebook.ipynb`\.

1. Optionally, choose **Tags**, and then add any additional key\-value tags for the notebook\.
**Important**  
A default tag with the **Key** string set to `creatorUserID` and the value set to your IAM user ID is applied for access purposes\. We recommend that you do not change or remove this tag because it can be used to control access\. For more information, see [Use Cluster and Notebook Tags with IAM Policies for Access Control](security_iam_emr-with-iam.md#emr-tag-based-access)\.