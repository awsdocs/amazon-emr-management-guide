# Establish access and permissions for Git\-based repositories<a name="emr-studio-enable-git"></a>

EMR Studio supports the following Git\-based services:
+ [AWS CodeCommit](http://aws.amazon.com/codecommit)
+ [GitHub](https://github.com)
+ [Bitbucket](https://bitbucket.org/)
+ [GitLab](https://about.gitlab.com/)

To let EMR Studio users associate a Git repository with a Workspace, set up the following access and permissions requirements\. You can also configure Git\-based repositories that you host in a private network by following the instructions in [Configure a privately hosted Git repository for EMR Studio](#emr-studio-private-git-repo)\.

**Cluster internet access**  
Both Amazon EMR clusters running on Amazon EC2 and Amazon EMR on EKS clusters attached to Studio Workspaces must be in a private subnet that uses a network address translation \(NAT\) gateway, or they must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC options](emr-clusters-in-a-vpc.md)\.  
The security groups that you use with EMR Studio must also include an outbound rule that allows Workspaces to route traffic to the internet from an attached EMR cluster\. For more information, see [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)\.  
If the network interface is in a public subnet, it won't be able to communicate with the internet through an internet gateway \(IGW\)\.

**Permissions for AWS Secrets Manager**  
To let EMR Studio users access Git repositories with secrets stored in AWS Secrets Manager, add a permissions policy to the [service role for EMR Studio](emr-studio-service-role.md) that allows the `secretsmanager:GetSecretValue` operation\.

For information about how to link Git\-based repositories to Workspaces, see [Link Git\-based repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.

## Configure a privately hosted Git repository for EMR Studio<a name="emr-studio-private-git-repo"></a>

Use the following instructions to configure privately hosted repositories for Amazon EMR Studio\. Provide a configuration file with information about your DNS and Git servers\. EMR Studio uses this information to configure Workspaces that can route traffic to your self\-managed repositories\.

**Prerequisites**

Before you configure a privately hosted Git repository for EMR Studio, you need the following:
+ A designated Amazon S3 storage location where EMR Studio can back up the Workspaces and notebook files in the Studio\. Use the same S3 bucket that you specify when you create a Studio\.

**To configure one or more privately hosted Git repositories for EMR Studio**

1. Create a configuration file using the following template\. Include the following values for each Git server that you want to specify in your configuration:
   + **`DnsServerIpV4`** \- The IPv4 address of your DNS server\. If you provide values for both `DnsServerIpV4` and `GitServerIpV4List`, the value for `DnsServerIpV4` takes precedence and EMR Studio uses `DnsServerIpV4` to resolve your `GitServerDnsName`\.
**Note**  
To use privately hosted Git repositories, your DNS server must allow inbound access from EMR Studio\. We urge you to secure your DNS server against other, unauthorized access\.
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

1. Upload the configuration file into the designated Amazon S3 storage location in a folder called `life-cycle-configuration`\. For example, if your default S3 location is `s3://DOC-EXAMPLE-BUCKET/studios`, your configuration file would be in `s3://DOC-EXAMPLE-BUCKET/studios/life-cycle-configuration/configuration.json`\.
**Important**  
We urge you to restrict access to your `life-cycle-configuration` folder to Studio administrators and to your EMR Studio service role, and that you secure `configuration.json` against unauthorized access\. For instructions, see [Controlling access to a bucket with user policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/walkthrough1.html) or [Security Best Practices for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)\.

   For upload instructions, see [Creating a folder](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-folders.html#create-folder) and [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon Simple Storage Service User Guide*\. To apply your configuration to an existing Workspace, close and restart the Workspace after you upload your configuration file to Amazon S3\.