# Step 2: Launch Your Sample Amazon EMR Cluster<a name="emr-gs-launch-sample-cluster"></a>

In this step, you launch your sample cluster by using **Quick Options** in the Amazon EMR console and leaving most options to their default values\. To learn more about these options, see [Summary of Quick Options](#emr-gs-quick-cluster-options) after the procedure\. You can also select **Go to advanced options** to explore the additional configuration options available for a cluster\. Before you create your cluster for this tutorial, make sure that you meet the requirements in [Step 1: Set Up Prerequisites for Your Sample Cluster](emr-gs-prerequisites.md)\.

## Launch the Sample Cluster<a name="emr-gs-launch-quick-cluster"></a><a name="emr-gs-launch-emr-cluster"></a>

**To launch the sample Amazon EMR cluster**

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. On the **Create Cluster \- Quick Options** page, accept the default values except for the following fields: 
   + Enter a **Cluster name** that helps you identify the cluster, for example, *My First EMR Cluster*\.
   + Under **Security and access**, choose the **EC2 key pair** that you created in [Create an Amazon EC2 Key Pair](emr-gs-prerequisites.md#emr-gs-key-pair)\.

1. Choose **Create cluster**\.

The cluster status page with the cluster **Summary** appears\. You can use this page to monitor the progress of cluster creation and view details about cluster status\. As cluster creation tasks finish, items on the status page update\. You may need to choose the refresh icon on the right or refresh your browser to receive updates\.

Under **Network and hardware**, find the **Master** and **Core** instance status\. The status goes from **Provisioning** to **Bootstrapping** to **Waiting** during the cluster creation process\. For more information, see [Understanding the Cluster Lifecycle](emr-overview.md#emr-overview-cluster-lifecycle)\.

As soon as you see the links for **Security groups for Master** and **Security Groups for Core & Task**, you can move on to the next step, but you may want to wait until the cluster starts successfully and is in the **Waiting** state\.

For more information about reading the cluster summary, see [View Cluster Status and Details](emr-manage-view-clusters.md)\.

## Summary of Quick Options<a name="emr-gs-quick-cluster-options"></a>

The following table describes the fields and default values when you launch a cluster using the **Quick cluster configuration** page in the Amazon EMR console\.


| Console field | Default value | Description | 
| --- | --- | --- | 
| Cluster name | My cluster | The cluster name is an optional, descriptive name for your cluster that does not need to be unique\. | 
| Logging | Enable | When logging is enabled, Amazon EMR writes detailed log data to the Amazon S3 folder specified\. Logging can only be enabled when you create the cluster and the setting can't be changed later\. A default Amazon S3 bucket is specified\. You can optionally specify your own\. For more information, see [View Log Files Archived to Amazon S3](emr-manage-view-web-log-files.md#emr-manage-view-web-log-files-s3)\. | 
| S3 folder | s3://aws\-logs\-account\_number\-region/elasticmapreduce/ | This option specifies the path to a folder in an Amazon S3 bucket where you want Amazon EMR to write log data\. If the default folder in the specified path does not exist in the bucket, it is created for you\. You can specify a different folder by typing or browsing to an Amazon S3 folder\.  | 
| Launch mode | Cluster | This option specifies whether to launch a long\-running cluster or a cluster that terminates after running any steps that you specify\. With the **Cluster** option, the cluster continues to run until you terminate it, which is called a long\-running cluster\. If you choose **Step execution**, Amazon EMR prompts you to add and configure steps\. You can use steps to submit work to a cluster\. After the steps that you specify finish executing, the cluster terminates automatically\. For more information, see [Configuring a Cluster to Auto\-Terminate or Continue](emr-plan-longrunning-transient.md)\.  | 
| Release | emr\-5\.23\.0 | This option specifies the Amazon EMR release version to use when the cluster is created\. The Amazon EMR release determines the version of open\-source applications, such as Hadoop and Hive, that Amazon EMR installs\. The label for the latest release version is selected by default\. You can select an earlier Amazon EMR release if you need different versions of open\-source applications for compatibility with your solution\. Some Amazon EMR features and applications may not be available when using earlier Amazon EMR release versions, so recommend that you use the latest release version whenever possible\. For more information about each Amazon EMR release version, see [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\. | 
| Applications | Core Hadoop | This option determines the open\-source applications from the big data ecosystem to install on your cluster\. The most common application combinations are available using quick start\. To select your own combination of applications, including additional applications not listed in quick start, choose **Go to advanced options**\. For information about the applications and versions available with each Amazon EMR release version, see [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\. In addition, if an application isn't available for Amazon EMR to install, or you need to install a custom application on all cluster instances, you can use a bootstrap action\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\. If you select **Step execution**, Amazon EMR chooses the applications to install based on what your steps require\. | 
| Instance type | m4\.large | This option determines the Amazon EC2 instance type that Amazon EMR initializes for the instances that run in your cluster\. The default instance selection varies by region and some instance types may not be available in some regions\. For more information, see [Configure Cluster Hardware and Networking](emr-plan-instances.md)\. | 
| Number of instances | 3 | This option determines the number of Amazon EC2 instances to initialize\. Each instance corresponds to a node in the Amazon EMR cluster\. You must have at least one node, which is the master node\. For guidance about choosing instance types and the number of instances, see [Cluster Configuration Guidelines and Best Practices](emr-plan-instances-guidelines.md)\. | 
| EC2 key pair | Choose an option | This specifies the Amazon EC2 key pair to use when connecting to the nodes in your cluster over a Secure Shell \(SSH\) connection\. We strongly recommend that you create and specify an Amazon EC2 key pair\. If you do not select a key pair, you cannot connect to the cluster to submit steps or interact with applications\. For more information, see [Connect to the Cluster](emr-connect-master-node.md)\. To connect, you also need to create an inbound rule in the security group to allow SSH connections\. | 
| Permissions | Default | Use this option to specify the AWS Identity and Access Management roles that the cluster uses\. These roles determine the permissions that Amazon EMR and the applications running on cluster instances have to interact with other AWS services\. You can choose **Custom** to specify your own roles\. We recommend using the default roles to start with\. For more information, see [Configure IAM Roles for Amazon EMR Permissions to AWS Services](emr-iam-roles.md)\. | 