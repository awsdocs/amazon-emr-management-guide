# Establish Access and Permissions for Git\-Based Repositories<a name="emr-studio-enable-git"></a>

EMR Studio supports the following Git\-based services:
+ [AWS CodeCommit](http://aws.amazon.com/codecommit)
+ [GitHub](https://github.com)
+ [Bitbucket](https://bitbucket.org/)
+ [Privately\-hosted GitLab](https://about.gitlab.com/)

To let your EMR Studio users associate a Git repository with a Workspace, set up the following access and permissions requirements\. You can also configure Git\-based repositories that you host in a private network by following the instructions in [Configure a Privately\-hosted Git Repository for EMR Studio](#emr-studio-private-git-repo)\.

**Cluster internet access**  
Both Amazon EMR clusters running on Amazon EC2 and Amazon EMR on EKS clusters attached to Studio Workspaces must be in a private subnet that uses a network address translation \(NAT\) gateway, or they must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC Options](emr-clusters-in-a-vpc.md)\.  
The security groups that you use with EMR Studio must also include an outbound rule that allows Workspaces to route traffic to the internet from an attached EMR cluster\. For more information, see [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)\.

**Permissions for AWS Secrets Manager**  
To let EMR Studio users access Git repositories with secrets stored in AWS Secrets Manager, add a permissions policy to the [service role for EMR Studio](emr-studio-service-role.md) that allows the `secretsmanager:GetSecretValue` action\.

For information about how to link Git\-based repositories to Workspaces, see [Link Git\-Based Repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.

## Configure a Privately\-hosted Git Repository for EMR Studio<a name="emr-studio-private-git-repo"></a>

Use the following instructions to configure privately\-hosted repositories for Amazon EMR Studio\. You must provide a configuration file with information about your DNS and Git servers\. EMR Studio uses this information to configure Workspaces that can route traffic to your self\-managed repositories\.

**Prerequisites**

Before you configure a privately\-hosted Git repository for EMR Studio, you must have the following:
+ A designated Amazon S3 storage bucket where EMR Studio can back up the Workspaces and notebook files in your Studio\. Use the same S3 bucket that you specify when you create a Studio\.

**To configure one or more privately\-hosted Git repositories for EMR Studio**

1. Create a configuration file using the provided template\. Include the following values for each Git server that you want to specify in your configuration:
   + **`DnsServerIpV4`** \- The IPv4 address of your DNS server\.
   + **`GitServerDnsName`** \- The DNS name of your Git server\. For example `"git.example.com"`\.
   + **`GitServerIpV4List`** \- A list of IPv4 addresses that belong to your Git servers\.

   ```
   [
       {
           "Type": "PrivatelyHostedGitConfig",
           "Value": [
               {
                   "DnsServerIpV4": "<10.24.34.xxx>",
                   "GitServerDnsName": "<enterprise.git.com>",
                   "GitServerIpV4List": [
                       "<xxx.xxx.xxx.xxx>",
                       "<xxx.xxx.xxx.xxx>"
                   ]
               },
               {
                   "DnsServerIpV4": "<10.24.34.xxx>",
                   "GitServerDnsName": "<git.example.com>",
                   "GitServerIpV4List": [
                       "<xxx.xxx.xxx.xxx>",
                       "<xxx.xxx.xxx.xxx>"
                   ]
               }
           ]
       }
   ]
   ```

1. Save your configuration file as `configuration.json`\.

1. Upload the configuration file into your designated Amazon S3 storage location in a folder called `life-cycle-configuration`\. For example, if your default S3 location is `s3://DOC-EXAMPLE-BUCKET/studios`, your configuration file should be located at `s3://DOC-EXAMPLE-BUCKET/studios/life-cycle-configuration/configuration.json`\.
**Important**  
We strongly recommend that you restrict access to your `life-cycle-configuration` folder to only Studio administrators and to your EMR Studio service role\. For instructions, see [Controlling access to a bucket with user policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/walkthrough1.html)\.

   For upload instructions, see [Creating a folder](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-folders.html#create-folder) and [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon Simple Storage Service User Guide*\. To apply your configuration to existing Workspaces, you should close and restart any open Workspaces after you upload your configuration file to Amazon S3\.