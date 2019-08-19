# Benefits of Using Amazon EMR<a name="emr-overview-benefits"></a>

There are many benefits to using Amazon EMR\. This section provides an overview of these benefits and links to additional information to help you explore further\.

**Topics**
+ [Cost Savings](#emr-benefits-cost)
+ [AWS Integration](#emr-benefits-integration)
+ [Deployment](#emr-benefits-deployment)
+ [Scalability and Flexibility](#emr-benefits-scalability)
+ [Reliability](#emr-benefits-reliability)
+ [Security](#emr-benefits-security)
+ [Monitoring](#emr-benefits-monitoring)
+ [Management Interfaces](#emr-what-tools)

## Cost Savings<a name="emr-benefits-cost"></a>

Amazon EMR pricing depends on the instance type and number of EC2 instances that you deploy and the region in which you launch your cluster\. On\-demand pricing offers low rates, but you can reduce the cost even further by purchasing Reserved Instances or Spot Instances\. Spot Instances can offer significant savings—as low as a tenth of on\-demand pricing in some cases\.

**Note**  
If you use Amazon S3, Amazon Kinesis, or DynamoDB with your EMR cluster, there are additional charges for those services that are billed separately from your Amazon EMR usage\.

For more information about pricing options and details, see [Amazon EMR Pricing](https://aws.amazon.com//elasticmapreduce/pricing/)\.

## AWS Integration<a name="emr-benefits-integration"></a>

Amazon EMR integrates with other AWS services to provide capabilities and functionality related to networking, storage, security, and so on, for your cluster\. The following list provides several examples of this integration:
+ Amazon EC2 for the instances that comprise the nodes in the cluster
+ Amazon Virtual Private Cloud \(Amazon VPC\) to configure the virtual network in which you launch your instances
+ Amazon S3 to store input and output data
+ Amazon CloudWatch to monitor cluster performance and configure alarms
+ AWS Identity and Access Management \(IAM\) to configure permissions
+ AWS CloudTrail to audit requests made to the service
+ AWS Data Pipeline to schedule and start your clusters

## Deployment<a name="emr-benefits-deployment"></a>

Your EMR cluster consists of EC2 instances, which perform the work that you submit to your cluster\. When you launch your cluster, Amazon EMR configures the instances with the applications that you choose, such as Apache Hadoop or Spark\. Choose the instance size and type that best suits the processing needs for your cluster: batch processing, low\-latency queries, streaming data, or large data storage\. For more information about the instance types available for Amazon EMR, see [Configure Cluster Hardware and Networking](emr-plan-instances.md)\.

Amazon EMR offers a variety of ways to configure software on your cluster\. For example, you can install an Amazon EMR release with a chosen set of applications that can include versatile frameworks, such as Hadoop, and applications, such as Hive, Pig, or Spark\. You can also install one of several MapR distributions\. Amazon EMR uses Amazon Linux, so you can also install software on your cluster manually using the yum package manager or from the source\. For more information, see [Configure Cluster Software](emr-plan-software.md)\.

## Scalability and Flexibility<a name="emr-benefits-scalability"></a>

Amazon EMR provides flexibility to scale your cluster up or down as your computing needs change\. You can resize your cluster to add instances for peak workloads and remove instances to control costs when peak workloads subside\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md)\.

 Amazon EMR also provides the option to run multiple instance groups so that you can use On\-Demand Instances in one group for guaranteed processing power together with Spot Instances in another group to have your jobs completed faster and for lower costs\. You can also mix different instance types to take advantage of better pricing for one Spot Instance type over another\. For more information, see [When Should You Use Spot Instances?](emr-plan-instances-guidelines.md#emr-plan-spot-instances)\. 

Additionally, Amazon EMR provides the flexibility to use several file systems for your input, output, and intermediate data\. For example, you might choose the Hadoop Distributed File System \(HDFS\) which runs on the master and core nodes of your cluster for processing data that you do not need to store beyond your cluster’s lifecycle\. You might choose the EMR File System \(EMRFS\) to use Amazon S3 as a data layer for applications running on your cluster so that you can separate your compute and storage, and persist data outside of the lifecycle of your cluster\. EMRFS provides the added benefit of allowing you to scale up or down for your compute and storage needs independently\. You can scale your compute needs by resizing your cluster and you can scale your storage needs by using Amazon S3\. For more information, see [Work with Storage and File Systems](emr-plan-file-systems.md)\.

## Reliability<a name="emr-benefits-reliability"></a>

Amazon EMR monitors nodes in your cluster and automatically terminates and replaces an instance in case of failure\.

Amazon EMR provides configuration options that control how your cluster is terminated—automatically or manually\. If you configure your cluster to be automatically terminated, it is terminated after all the steps complete\. This is referred to as a transient cluster\. However, you can configure the cluster to continue running after processing completes so that you can choose to terminate it manually when you no longer need it\. Or, you can create a cluster, interact with the installed applications directly, and then manually terminate the cluster when you no longer need it\. The clusters in these examples are referred to as *long\-running clusters*\. 

Additionally, you can configure termination protection to prevent instances in your cluster from being terminated due to errors or issues during processing\. When termination protection is enabled, you can recover data from instances before termination\. The default settings for these options differ depending on whether you launch your cluster by using the console, CLI, or API\. For more information, see [Using Termination Protection](UsingEMR_TerminationProtection.md)\.

## Security<a name="emr-benefits-security"></a>

Amazon EMR leverages other AWS services, such as IAM and Amazon VPC, and features such as Amazon EC2 key pairs, to help you secure your clusters and data\.

### IAM<a name="emr-benefits-iam"></a>

Amazon EMR integrates with IAM to manage permissions\. You define permissions using IAM policies, which you attach to IAM users or IAM groups\. The permissions that you define in the policy determine the actions that those users or members of the group can perform and the resources that they can access\. For more information, see [How Amazon EMR Works with IAM](security_iam_emr-with-iam.md)\.

Additionally, Amazon EMR uses IAM roles for the Amazon EMR service itself and the EC2 instance profile for the instances\. These roles grant permissions for the service and instances to access other AWS services on your behalf\. There is a default role for the Amazon EMR service and a default role for the EC2 instance profile\. The default roles use AWS managed policies, which are created for you automatically the first time you launch an EMR cluster from the console and choose default permissions\. You can also create the default IAM roles from the AWS CLI\. If you want to manage the permissions instead of AWS, you can choose custom roles for the service and instance profile\. For more information, see [Configure IAM Service Roles for Amazon EMR Permissions to AWS Services and Resources](emr-iam-roles.md)\.

### Security Groups<a name="emr-benefits-security-groups"></a>

Amazon EMR uses security groups to control inbound and outbound traffic to your EC2 instances\. When you launch your cluster, Amazon EMR uses a security group for your master instance and a security group to be shared by your core/task instances\. Amazon EMR configures the security group rules to ensure communication among the instances in the cluster\. Optionally, you can configure additional security groups and assign them to your master and core/task instances for more advanced rules\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.

### Encryption<a name="emr-benefits-encryption"></a>

Amazon EMR supports optional Amazon S3 server\-side and client\-side encryption with EMRFS to help protect the data that you store in Amazon S3\. With server\-side encryption, Amazon S3 encrypts your data after you upload it\.

With client\-side encryption, the encryption and decryption process occurs in the EMRFS client on your EMR cluster\. You manage the master key for client\-side encryption using either the AWS Key Management Service \(AWS KMS\) or your own key management system\.

For more information, see [Encryption for Amazon S3 Data with EMRFS in the *Amazon EMR Release Guide*](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-encryption-s3.html)\.

### Amazon VPC<a name="emr-benefits-vpc"></a>

Amazon EMR supports launching clusters in a virtual private cloud \(VPC\) in Amazon VPC\. A VPC is an isolated, virtual network in AWS that provides the ability to control advanced aspects of network configuration and access\. For more information, see [Configure Networking](emr-plan-vpc-subnet.md)\.

### AWS CloudTrail<a name="emr-benefits-cloudtrail"></a>

Amazon EMR integrates with CloudTrail to log information about requests made by or on behalf of your AWS account\. With this information, you can track who is accessing your cluster when, and the IP address from which they made the request\. For more information, see [Logging Amazon EMR API Calls in AWS CloudTrail ](logging_emr_api_calls.md)\.

### Amazon EC2 Key Pairs<a name="emr-benefits-key-pairs"></a>

You can monitor and interact with your cluster by forming a secure connection between your remote computer and the master node\. You use the Secure Shell \(SSH\) network protocol for this connection or use Kerberos for authentication\. If you use SSH, an Amazon EC2 key pair is required\. For more information, see [Use an Amazon EC2 Key Pair for SSH Credentials](emr-plan-access-ssh.md)\.

## Monitoring<a name="emr-benefits-monitoring"></a>

You can use the Amazon EMR management interfaces and log files to troubleshoot cluster issues, such as failures or errors\. Amazon EMR provides the ability to archive log files in Amazon S3 so you can store logs and troubleshoot issues even after your cluster terminates\. Amazon EMR also provides an optional debugging tool in the Amazon EMR console to browse the log files based on steps, jobs, and tasks\. For more information, see [Configure Cluster Logging and Debugging](emr-plan-debugging.md)\.

Amazon EMR integrates with CloudWatch to track performance metrics for the cluster and jobs within the cluster\. You can configure alarms based on a variety of metrics such as whether the cluster is idle or the percentage of storage used\. For more information, see [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\.

## Management Interfaces<a name="emr-what-tools"></a>

 There are several ways you can interact with Amazon EMR: 
+ **Console** — A graphical user interface that you can use to launch and manage clusters\. With it, you fill out web forms to specify the details of clusters to launch, view the details of existing clusters, debug, and terminate clusters\. Using the console is the easiest way to get started with Amazon EMR; no programming knowledge is required\. The console is available online at [https://console\.aws\.amazon\.com//elasticmapreduce/home](https://console.aws.amazon.com//elasticmapreduce/home)\. 
+ **AWS Command Line Interface \(AWS CLI\)** — A client application you run on your local machine to connect to Amazon EMR and create and manage clusters\. The AWS CLI contains a feature\-rich set of commands specific to Amazon EMR\. With it, you can write scripts that automate the process of launching and managing clusters\. If you prefer working from a command line, using the AWS CLI is the best option\. For more information, see [Amazon EMR](https://docs.aws.amazon.com/cli/latest/reference/emr/index.html) in the *AWS CLI Command Reference*\.
+ **Software Development Kit \(SDK\)** — SDKs provide functions that call Amazon EMR to create and manage clusters\. With them, you can write applications that automate the process of creating and managing clusters\. Using the SDK is the best option to extend or customize the functionality of Amazon EMR\. Amazon EMR is currently available in the following SDKs: Go, Java, \.NET \(C\# and VB\.NET\), Node\.js, PHP, Python, and Ruby\. For more information about these SDKs, see [Tools for AWS](https://aws.amazon.com/tools/) and [Amazon EMR Sample Code & Libraries](https://aws.amazon.com//code/Elastic-MapReduce)\. 
+ **Web Service API** — A low\-level interface that you can use to call the web service directly, using JSON\. Using the API is the best option to create a custom SDK that calls Amazon EMR\. For more information, see the [Amazon EMR API Reference](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/Welcome.html)\. 