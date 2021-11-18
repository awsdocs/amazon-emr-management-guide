# Considerations when using EMR Notebooks<a name="emr-managed-notebooks-considerations"></a>

Consider the following requirements when you create clusters and develop solutions using EMR notebook\.

## Cluster requirements<a name="considerations-limitations"></a>
+ **Enable Amazon EMR Block Public Access** – Inbound access to a cluster enables cluster users to execute notebook kernels\. Ensure that only authorized users can access the cluster\. We strongly recommend that you leave block public access enabled, and that you limit inbound SSH traffic to only trusted sources\. For more information, see [Using Amazon EMR block public access](emr-block-public-access.md) and [Control network traffic with security groups](emr-security-groups.md)\.
+ **Use a Compatible Cluster** – A cluster attached to a notebook must meet the following requirements:
  + Only clusters created using Amazon EMR are supported\. You can create a cluster independently within Amazon EMR and then attach an EMR notebook, or you can create a compatible cluster when you create an EMR notebook\.
  + Only clusters created using Amazon EMR release version 5\.18\.0 and later are supported\. See [Differences in capabilities by cluster release version](#considerations-cluster-version)\.
  + Clusters created using Amazon EC2 instances with AMD EPYC processors—for example, m5a\.\* and r5a\.\* instance types—are not supported\.
  + EMR Notebooks works only with clusters created with `VisibleToAllUsers` set to `true`\. `VisibleToAllUsers` is `true` by default\.
  + The cluster must be launched within an EC2\-VPC\. Public and private subnets are supported\. The EC2\-Classic platform is not supported\.
  + The cluster must be launched with Hadoop, Spark, and Livy installed\. Other applications may be installed, but EMR Notebooks currently supports Spark clusters only\.
**Important**  
For EMR release versions 5\.32\.0 and later, or 6\.2\.0 and later, your cluster must also be running the Jupyter Enterprise Gateway application in order to work with EMR Notebooks\.
  + Clusters using Kerberos authentication are not supported\.
  + Clusters integrated with AWS Lake Formation support the installation of notebook\-scoped libraries only\. Installing kernels and libraries on the cluster are not supported\.
  + Clusters with multiple master nodes are not supported\.
  + Clusters using Amazon EC2 instances based on AWS Graviton2 are not supported\.

## Differences in capabilities by cluster release version<a name="considerations-cluster-version"></a>

We strongly recommend that you use EMR Notebooks with clusters created using Amazon EMR release versions 5\.30\.0, 5\.32\.0 or later, or 6\.2\.0 or later\. With these versions, EMR Notebooks runs kernels on the attached Amazon EMR cluster\. Kernels and libraries can be installed directly on the cluster master node\. Using EMR Notebooks with these cluster versions has the following benefits:
+ **Improved performance** – Notebook kernels run on clusters with EC2 instance types that you select\. Earlier versions run kernels on a specialized instance that cannot be resized, accessed, or customized\. 
+ **Ability to add and customize kernels** – You can connect to the cluster to install kernel packages using `conda` and `pip`\. In addition, `pip` installation is supported using terminal commands within notebook cells\. In earlier versions, only pre\-installed kernels were available \(Python, PySpark, Spark, and SparkR\)\. For more information, see [Installing kernels and Python libraries on a cluster master node](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel)\.
+ **Ability to install Python libraries** – You can [install Python libraries on the cluster master node](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel) using `conda` and `pip`\. We recommend using `conda`\. With earlier versions, only [notebook\-scoped libraries](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-scoped-libraries) for PySpark are supported\.


**Supported EMR Notebooks features by cluster release**  

| Cluster release version | Notebook\-scoped libraries for PySpark | Kernel installation on cluster | Python library installation on master node | 
| --- | --- | --- | --- | 
|  Earlier than 5\.18\.0  |  EMR Notebooks not supported  | 
|  5\.18\.0–5\.25\.0  |  No  |  No  |  No  | 
|  5\.26\.0–5\.29\.0  |  [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-scoped-libraries)  |  No  |  No  | 
|  5\.30\.0  |  [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-scoped-libraries)  |  [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel)  |  [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel)  | 
|  6\.0\.0  |  No  |  No  |  No  | 
| 5\.32\.0 and later, and 6\.2\.0 and later | [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-scoped-libraries) | [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel) | [Yes](emr-managed-notebooks-installing-libraries-and-kernels.md#emr-managed-notebooks-cluster-kernel) | 

## Limits for concurrently attached EMR Notebooks<a name="emr-managed-notebooks-cluster-limits"></a>

When you create a cluster that supports notebooks, consider the EC2 Instance type of the cluster master node\. The memory constraints of this EC2 Instance determine the number of notebooks that can be ready simultaneously to run code and queries on the cluster\.


| Master node EC2 instance type | Number of EMR Notebooks | 
| --- | --- | 
|  \*\.medium  |  2  | 
|  \*\.large  |  4  | 
|  \*\.xlarge  |  8  | 
|  \*\.2xlarge  |  16  | 
|  \*\.4xlarge  |  24  | 
|  \*\.8xlarge  |  24  | 
|  \*\.16xlarge  |  24  | 

## Jupyter Notebook and Python versions<a name="considerations-versions"></a>

EMR Notebooks runs [Jupyter Notebook version 6\.0\.2](https://jupyter-notebook.readthedocs.io/en/stable/changelog.html#release-6-0-2) and Python 3\.6\.5 regardless of the Amazon EMR release version of the attached cluster\.

## <a name="considerations-s3-encryption"></a>

If you specify an encrypted location in Amazon S3 to store notebook files, you must set up the [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md) as a key user\. The default service role is `EMR_Notebooks_DefaultRole`\. If you are using an AWS KMS key for encryption, see [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-users-crypto) in the AWS Key Management Service Developer Guide and the [support article for adding key users](https://aws.amazon.com/premiumsupport/knowledge-center/s3-bucket-access-default-encryption/)\.