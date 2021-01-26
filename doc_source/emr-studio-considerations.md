# Considerations and Limitations<a name="emr-studio-considerations"></a>

Consider the following when you work with Amazon EMR Studio:
+ EMR Studio \(preview\) is currently available in the following AWS Regions: US East \(Ohio, N\. Virginia\), US West \(Oregon\), Asia Pacific \(Mumbai\), and EU \(Ireland\)\. Amazon EMR on EKS is only available in US East \(Ohio, N\. Virginia\), US West \(Oregon\), and EU \(Ireland\)\.
+ For EMR Studio \(preview\), you must set up and use the AWS CLI to create Studios\.
+ EMR Studio does not currently support using self\-managed Microsoft Active Directory or AWS Managed Microsoft AD with AWS Single Sign\-On\. Azure AD is supported\.
+ You can specify an Amazon Virtual Private Cloud \(Amazon VPC\) and a maximum of five subnets for an EMR Studio\. Studio users select one of the subnets to associate with a Workspace\. A Workspace can only access EMR computing resources in the selected subnet\. For more information about VPCs for Amazon EMR, see [Amazon VPC Options](emr-clusters-in-a-vpc.md)\.
+ To let users automatically provision new EMR clusters running on Amazon EC2 for a Workspace, associate an EMR Studio with a set of cluster templates\. Administrators can define cluster templates with AWS Service Catalog and can choose whether a user or group can access all of the cluster templates, or no cluster templates, within a Studio\.
+ You must associate an EMR Studio with an IAM user role, which is assumed by users and groups logged in to a Studio\. To refine permissions, you must apply IAM session policies to individual users or groups\.
+ When you define access permissions to notebook files stored in Amazon S3 or read secrets from AWS Secrets Manager, you should use the EMR service role\. Defining these permissions using session policies is not currently supported\.
+ A user can only choose from the subnets that are specified at Studio creation time when creating a Workspace\. 
+ You can create multiple EMR Studios to control access to EMR clusters that are in different VPCs or subnets\.
+ You must use the AWS CLI to set up Amazon EMR on EKS clusters\. You can then use the Studio interface to attach clusters to Workspaces in order to run notebook jobs across multiple Availability Zones within a single AWS Region\.
+ Amazon EMR on EKS currently supports creating managed endpoints using the following Amazon EMR release labels: emr\-6\.2\.0\-latest\.

## Known Issues<a name="emr-studio-known-issues"></a>
+ Kernels that run on Amazon EMR on EKS clusters occasionally fail to start due to timeout issues\. If you encounter an error or issue starting the kernel, you should close your notebook file, shut down the kernel, and then reopen the notebook file\.
+ The **Restart kernel** operation does not work as expected when a Workspace is attached to an Amazon EMR on EKS cluster\. After you select **Restart kernel**, refresh your page for the restart to take effect\.
+ If a Workspace is not attached to a cluster, an error message appears when a Studio user opens a notebook file and tries to select a kernel\. You can ignore this error message by choosing **Ok**, but you must attach the Workspace to a cluster and select a kernel before you can run notebook code\.
+ When you use Amazon EMR 6\.2\.0 with a [security configuration](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-configurations.html) to set up cluster security, the Workspace interface appears blank and does not work as expected\. We recommend that you use a different supported version of EMR if you want to configure data encryption or Amazon S3 authorization for EMRFS for a cluster\. EMR Studio works with Amazon EMR versions 5\.32\.0 \(EMR 5\.x series\) or 6\.2\.0 \(EMR 6\.x series\) and later\.

## Feature Limitations<a name="emr-studio-limitations"></a>

Amazon EMR Studio does not support the following Amazon EMR features:
+ Attaching and running jobs on EMR clusters that are created with a security configuration that specifies Kerberos authentication
+ Accessing an EMR Studio using the AWS Management Console
+ Clusters with multiple master nodes
+ Clusters integrated with AWS Lake Formation

## Service Limits for EMR Studio \(Preview\)<a name="emr-studio-default-limits"></a>

The following table provides the service limits for EMR Studio during the preview period\.


****  

| Item | Limit | 
| --- | --- | 
| EMR Studios | Maximum of 10 per AWS account | 
| Subnets | Maximum of 5 associated with each EMR Studio | 
| AWS SSO Groups | Maximum of 5 assigned to each EMR Studio | 
| AWS SSO Users | Maximum of 100 assigned to each EMR Studio | 

## Cluster Requirements for Amazon EMR Studio<a name="emr-studio-cluster-requirements"></a>

All Amazon EMR clusters running on Amazon EC2 that you create for an EMR Studio Workspace must meet the following requirements\. Any clusters that you create using the EMR Studio interface automatically meet these requirements\.
+ Only clusters created using Amazon EMR versions 5\.32\.0 \(EMR 5\.x series\) or 6\.2\.0 \(EMR 6\.x series\) and later are supported\. You can create a cluster using the EMR console, AWS Command Line Interface, or SDK, and then attach it to an EMR Studio Workspace in the same Amazon Virtual Private Cloud subnet\. Alternatively, Studio users can create and attach a cluster when creating or working in an Amazon EMR Workspace\. For more information, see [Attach a Cluster to Your Workspace](emr-studio-create-use-clusters.md)\.
+ The cluster must be created with the `VisibleToAllUsers` property set to true\. For more information, see [Understanding the EMR Cluster VisibleToAllUsers Setting](security_iam_emr-with-iam.md#security_set_visible_to_all_users)\.
+ The cluster must be launched within an Amazon Virtual Private Cloud\. The EC2\-Classic platform is not supported\.
+ The cluster must be launched with Spark, Livy, and Jupyter Enterprise Gateway installed\. Other applications may be installed, but EMR Studio currently supports Spark clusters only\. 
+ To link Workspaces that are attached to a cluster to Git\-based repositories, the cluster must be in a private subnet\.

We strongly recommend that you leave Amazon EMR Block Public Access enabled, and that you limit inbound SSH traffic to only trusted sources\. Inbound access to a cluster lets users run notebooks on the cluster\. For more information, see [Using Amazon EMR Block Public Access](emr-block-public-access.md) and [Control Network Traffic with Security Groups](emr-security-groups.md)\.

In addition to EMR clusters running on Amazon EC2, you can set up and manage Amazon EMR on EKS clusters for EMR Studio using the AWS CLI\. EMR on EKS clusters must be set up using the following guidelines:
+ You must create a managed HTTPS endpoint for your Amazon EMR on EKS cluster to which Workspaces can attach\. The Amazon Elastic Kubernetes Service \(EKS\) cluster that you use to register your virtual cluster must have a private subnet to enable managed endpoints\.
+ To link Workspaces that are attached to an Amazon EMR on EKS cluster to Git\-based repositories, your cluster must be registered using an Amazon EKS cluster with a private subnet\.

For more information, see [Set Up Amazon EMR on EKS for Your Studio](emr-studio-create-eks-cluster.md)\.