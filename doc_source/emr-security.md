# Security in Amazon EMR<a name="emr-security"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that is built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](https://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to Amazon EMR, see [AWS Services in Scope by Compliance Program](https://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your company’s requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using Amazon EMR\. When you develop solutions on Amazon EMR, use the following technologies to help secure cluster resources and data according to your business requirements\. The topics in this chapter show you how to configure Amazon EMR and use other AWS services to meet your security and compliance objectives\.

## Security Configurations<a name="w19aac21c11"></a>

Security configurations in Amazon EMR are templates for different security setups\. You can create a security configuration to conveniently re\-use a security setup whenever you create a cluster\. For more information, see [Use Security Configurations to Set Up Cluster Security](emr-security-configurations.md)\.

## Data Protection<a name="w19aac21c13"></a>

You can implement data encryption to help protect data at rest in Amazon S3, data at rest in cluster instance storage, and data in transit\. For more information, see [Encrypt Data at Rest and in Transit](emr-data-encryption.md)\.

## AWS Identity and Access Management with Amazon EMR<a name="w19aac21c15"></a>

AWS Identity and Access Management \(IAM\) is an AWS service that helps an administrator securely control access to AWS resources\. IAM administrators control who can be *authenticated* \(signed in\) and *authorized* \(have permissions\) to use Amazon EMR resources\. IAM is an AWS service that you can use with no additional charge\.
+ **IAM Identity\-Based Policies** – IAM policies allow or deny permissions for IAM users and groups to perform actions\. Policies can be combined with tagging to control access on a cluster\-by\-cluster basis\. For more information, see [AWS Identity and Access Management for Amazon EMR](emr-plan-access-iam.md)\.
+ **IAM roles** – The Amazon EMR service role, instance profile, and service\-linked role control how Amazon EMR is able to access other AWS services\. For more information, see [Configure IAM Service Roles for Amazon EMR Permissions to AWS Services and Resources](emr-iam-roles.md)\.
+ **IAM roles for EMRFS requests to Amazon S3** – When Amazon EMR accesses Amazon S3, you can specify the IAM role to use based on the user, group, or the location of EMRFS data in Amazon S3\. This allows you to precisely control whether cluster users can access files from within Amazon EMR\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.

## Kerberos<a name="w19aac21c17"></a>

You can set up Kerberos to provide strong authentication through secret\-key cryptography\. For more information, see [Use Kerberos Authentication](emr-kerberos.md)\.

## Lake Formation<a name="w19aac21c19"></a>

You can use Lake Formation permissions together with the AWS Glue Data Catalog to provide fine\-grained, column\-level access to databases and tables in the AWS Glue Data Catalog\. Lake Formation enables federated single sign\-on to EMR Notebooks or Apache Zeppelin from an enterprise identity system\. For more information, see [Integrating Amazon EMR with AWS Lake Formation \(Beta\)](emr-lake-formation.md)\.

## Secure Socket Shell \(SSH\)<a name="w19aac21c21"></a>

SSH helps provide a secure way for users to connect to the command line on cluster instances\. It also provides tunneling to view web interfaces that applications host on the master node\. Clients can authenticate using Kerberos or an Amazon EC2 key pair\. For more information, see [Use an Amazon EC2 Key Pair for SSH Credentials](emr-plan-access-ssh.md) and [Connect to the Cluster](emr-connect-master-node.md)\.

## Amazon EC2 Security Groups<a name="w19aac21c23"></a>

Security groups act as a virtual firewall for EMR cluster instances, limiting inbound and outbound network traffic\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.

## Updates to the default Amazon Linux AMI for Amazon EMR<a name="w19aac21c25"></a>

When an Amazon EC2 instance in a cluster that is based on the default Amazon Linux AMI for Amazon EMR boots for the first time, critical security updates are installed by default\. Other updates are not installed\. Depending on the security posture of your application and the length of time that a cluster runs, you may choose to periodically reboot your cluster to apply security updates, or create a bootstrap action to customize package installation and updates\. You may also choose to test and then install select security updates on running cluster instances\. For more information, see [Using the Default Amazon Linux AMI for Amazon EMR](emr-default-ami.md)\.