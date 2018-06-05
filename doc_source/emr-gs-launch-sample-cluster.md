# Step 2: Launch Your Sample Amazon EMR Cluster<a name="emr-gs-launch-sample-cluster"></a>

 In this step, you launch your sample cluster by using the Amazon EMR console\. Before you perform this step, make sure that you meet the requirements in [Step 1: Set Up Prerequisites for Your Sample Cluster](emr-gs-prerequisites.md)\.

**Topics**
+ [Using Quick Cluster Configuration Overview](#emr-gs-quick-cluster-options)
+ [Launch the Sample Cluster](#emr-gs-launch-quick-cluster)

## Using Quick Cluster Configuration Overview<a name="emr-gs-quick-cluster-options"></a>

The following table describes the fields and default values when you launch a cluster using the **Quick cluster configuration** page in the Amazon EMR console\.


| Console field | Default value | Description | 
| --- | --- | --- | 
| Cluster name | My cluster | The cluster name is an optional, descriptive name for your cluster that does not need to be unique\. | 
| Logging | Enable | This option specifies whether to enable or disable logging\. When logging is enabled, Amazon EMR writes detailed log data to a folder in either a S3 bucket chosen for you or your own specified bucket\. Logging is an immutable property that can only be enabled when you create the cluster\. | 
| S3 folder | s3://aws\-logs\-account\_number\-region/elasticmapreduce/ | This option specifies the path to a folder in an S3 bucket where you want Amazon EMR to write log data\. The following example shows a path to an S3 bucket for AWS account ID 111122223333 in the us\-east\-1 region: s3://aws\-logs\-111122223333\-us\-east\-1/elasticmapreduce/\.   If the folder in the specified path does not exist in the bucket, it is created for you\. You can specify a different folder by typing or browsing to a different location\.  | 
| Launch mode | Cluster | This option specifies whether to launch an ongoing cluster or a transient cluster as follows:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs-launch-sample-cluster.html)  | 
| Release | EMR release label | This option specifies the software and Amazon EMR platform components, such as EMRFS, to install on your cluster\. Amazon EMR uses the release to initialize the Amazon EC2 instances on which your cluster runs\. These releases are specific to Amazon EMR and can be used only in the context of running your Amazon EMR cluster\. The latest release label is selected by default\. | 
| Applications | **All applications** \(for **Cluster** launch mode\) Core Hadoop \(if you choose Step execution launch mode\) | This option determines the applications to install on your cluster\. If you chose **Cluster** launch mode, you can select the applications to install\. If you chose **Step execution** launch mode, the list of applications is determined by the steps that you added\. | 
| Instance type | m4\.large | This option determines the Amazon EC2 instance type that Amazon EMR initializes for the instances that run in your cluster\. | 
| Number of instances | 3 | This option determines the number of Amazon EC2 instances to initialize\. Each instance corresponds to a node in the Amazon EMR cluster\. You must have at least one node\. | 
| EC2 key pair | Select an option | This option specifies the Amazon EC2 key pair to use when connecting to the nodes in your cluster using Secure Shell \(SSH\)\. If you do not select a key pair, you cannot connect to the cluster\. | 
| Permissions | Default | This option configures permissions for your Amazon EMR cluster\. These permissions are granted using policies that are applied to the following IAM roles:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs-launch-sample-cluster.html) With **Default** permissions, the IAM roles use the following AWS managed policies: **AmazonElasticMapReduceRole** for the Amazon EMR service and **AmazonElasticMapReduceforEC2Role** for your instance profile\. You can choose **View policy for EMR role** or **View policy for EC2 instance profile** to view these policies\.  With **Custom** permissions, you must select existing roles\. The policies attached to those roles determine the permissions for Amazon EMR and your Amazon EC2 instance profile\.   | 

## Launch the Sample Cluster<a name="emr-gs-launch-quick-cluster"></a>

Perform the following steps to launch your sample cluster\. Unless otherwise specified in the procedure, use the default values as described in the preceding table\.<a name="emr-gs-launch-emr-cluster"></a>

**To launch an Amazon EMR cluster**

Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1.  On the **Quick cluster configuration** page, accept the default values except for the following fields: 
   + For **S3 folder**, choose the folder icon to select the path to the `logs` folder that you created in [Create an Amazon S3 Bucket](emr-gs-prerequisites.md#emr-gs-create-bucket)\. 
   + For **EC2 key pair**, choose the key pair that you created in [Create an Amazon EC2 Key Pair](emr-gs-prerequisites.md#emr-gs-key-pair)\.

1. Choose **Create cluster**\. 

1. Proceed to the next step\. 