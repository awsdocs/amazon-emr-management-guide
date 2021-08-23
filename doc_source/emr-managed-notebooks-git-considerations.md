# Prerequisites and considerations<a name="emr-managed-notebooks-git-considerations"></a>

Consider the following when planning to integrate a Git\-based repository with EMR Notebooks\.

## AWS CodeCommit<a name="code-commit-considerations"></a>

If you use a CodeCommit repository, you must use Git credentials and HTTPS with CodeCommit\. SSH Keys, and HTTPS with the AWS CLI credential helper are not supported\. CodeCommit does not support personal access tokens \(PATs\)\. For more information, see [Using IAM with CodeCommit: Git credentials, SSH keys, and AWS access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html) in the *IAM User Guide* and [Setup for HTTPS users using Git credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) in the *AWS CodeCommit User Guide*\.

## Access and permission considerations<a name="access-considerations"></a>

Before associating a repository with your notebook, make sure that your cluster, IAM role for EMR Notebooks, and security groups have the correct settings and permissions\. You can also configure Git\-based repositories that you host in a private network by following the instructions in [Configure a privately\-hosted Git repository for EMR Notebooks](#emr-managed-notebooks-private-git-repo)\.
+ **Cluster internet access** – The network interface that is launched has only a private IP address\. This means that the cluster that your notebook connects to must be in a private subnet with a network address translation \(NAT\) gateway or must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-clusters-in-a-vpc.html)\.

  The security groups for your notebook must include an outbound rule that allows the notebook to route traffic to the internet from the cluster\. We recommend that you create your own security groups\. For more information, see [Specifying EC2 security groups for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-security-groups.html)\.
**Important**  
If the network interface is launched into a public subnet, it won't be able to communicate with the internet through an internet gateway \(IGW\)\.
+ **Permissions for AWS Secrets Manager** – If you use Secrets Manager to store secrets that you use to access a repository, the [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md) must have a permissions policy attached that allows the `secretsmanager:GetSecretValue` action\.

## Configure a privately\-hosted Git repository for EMR Notebooks<a name="emr-managed-notebooks-private-git-repo"></a>

Use the following instructions to configure privately\-hosted repositories for EMR Notebooks\. You must provide a configuration file with information about your DNS and Git servers\. Amazon EMR uses this information to configure EMR notebooks that can route traffic to your privately\-hosted repositories\.

**Prerequisites**

Before you configure a privately\-hosted Git repository for EMR Notebooks, you must have the following:
+ An Amazon S3 Control location where files for your EMR notebook will be saved\.

**To configure one or more privately\-hosted Git repositories for EMR Notebooks**

1. Create a configuration file using the provided template\. Include the following values for each Git server that you want to specify in your configuration:
   + **`DnsServerIpV4`**\- The IPv4 address of your DNS server\. If you provide values for both `DnsServerIpV4` and `GitServerIpV4List`, the value for `DnsServerIpV4` takes precedence and will be used to resolve your `GitServerDnsName`\.
**Note**  
To use privately\-hosted Git repositories, your DNS server must allow inbound access from EMR Notebooks\. We strongly recommend that you secure your DNS server against other, unauthorized access\.
   + **`GitServerDnsName`** \- The DNS name of your Git server\. For example `"git.example.com"`\.
   + **`GitServerIpV4List`** \- A list of IPv4 addresses that belong to your Git server\(s\)\.

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

1. Upload the configuration file into your designated Amazon S3 storage location in a folder called `life-cycle-configuration`\. For example, if your default S3 location is `s3://DOC-EXAMPLE-BUCKET/notebooks`, your configuration file should be located at `s3://DOC-EXAMPLE-BUCKET/notebooks/life-cycle-configuration/configuration.json`\.
**Important**  
We strongly recommend that you restrict access to your `life-cycle-configuration` folder to only your EMR Notebooks administrators, and to the service role for EMR Notebooks\. You should also secure `configuration.json` against unauthorized access\. For instructions, see [Controlling access to a bucket with user policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/walkthrough1.html) or [Security Best Practices for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)\.

   For upload instructions, see [Creating a folder](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-folders.html#create-folder) and [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon Simple Storage Service User Guide*\.