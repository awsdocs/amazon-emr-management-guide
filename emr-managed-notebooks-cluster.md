# Creating Amazon EMR Clusters for Notebooks<a name="emr-managed-notebooks-cluster"></a>

When you create a notebook or change clusters, you can have Amazon EMR create a new cluster along with the notebook, or you can choose a cluster that was created earlier\. Creating a new cluster with a notebook is a quick way to get started\. Creating a cluster beforehand is useful if you need to install additional applications, connect to the cluster using SSH, or need other customizations that Amazon EMR offers when you create a cluster, such as user impersonation\.

## Notebook Limits Per Cluster<a name="emr-managed-notebooks-cluster-limits"></a>

When you create a cluster that supports notebooks, consider the EC2 Instance type of the cluster master node\. The memory constraints of this EC2 instance determine the number of notebooks that can be ready simultaneously to run code and queries on the cluster\.


| Master Node EC2 Instance Type | Number of Notebooks | 
| --- | --- | 
|  \*\.medium  |  2  | 
|  \*\.large  |  4  | 
|  \*\.xlarge  |  8  | 
|  \*\.2xlarge  |  16  | 
|  \*\.4xlarge  |  24  | 
|  \*\.8xlarge  |  24  | 
|  \*\.16xlarge  |  24  | 

## Creating a Cluster When You Create a Notebook<a name="emr-managed-notebooks-new-cluster"></a>

When you have Amazon EMR create a cluster when you create an EMR notebook, the cluster has the following characteristics and limitations:
+ It uses the most recent Amazon EMR release version and the versions of Hadoop, Spark, and Livy included with that release version\. For more information, see the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\.
+ It is created without an EC2 key pair, so you are unable to connect to cluster EC2 instances using SSH\. If an SSH connection is required, create a cluster first and then specify it when you create an EMR notebook\.
+ It uses On\-Demand Instances, and the same instance type for all instances\. One instance is used for the master node, and the remainder are used for core nodes\. 
+ It uses the uniform instance groups configuration\. For more information, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-group-configuration.html) in the *Amazon EMR Management Guide*\.
+ It is launched in the default VPC for the AWS account\.

You can specify a custom AWS service role and security groups, if required\. For more information, see [Specifying the AWS Service Role](emr-managed-notebooks-service-role.md) and [Specifying EC2 Security Groups for EMR Notebooks and Clusters](emr-managed-notebooks-security-groups.md)\. If you need additional customization or different settings, create a cluster beforehand using Amazon EMR, and then specify that cluster when you create the notebook\.

## Using an Existing Amazon EMR Cluster<a name="emr-managed-notebooks-existing-cluster"></a>

EMR Notebooks supports clusters created only by using Amazon EMR\. You can create a cluster using Amazon EMR if you require more processing power, storage, or any of the extensive cluster customization features that Amazon EMR offers\. For more information about creating clusters, see [Plan and Configure Clusters](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan.html) in the *Amazon EMR Management Guide*\.

A cluster must meet the following requirements to be used with EMR Notebooks:
+ The cluster must be launched within an EC2\-VPC\. Public and private subnets are supported\. The EC2\-Classic platform is not supported\.
+ The cluster must be created using Amazon EMR release version 5\.18\.0 or later\.
+ The cluster must be launched with Hadoop, Spark, and Livy installed\. Other applications may be installed, but EMR Notebooks currently supports Spark clusters only\.
+ Clusters using Kerberos authentication are not supported\.