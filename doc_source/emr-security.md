# Security<a name="emr-security"></a>

Amazon EMR provides several features to help secure cluster resources and data:
+ **AWS Identity and Access Management \(IAM\) policies**

  IAM policies allow or deny permissions for IAM users and groups to perform actions\. Policies can be combined with tagging to control access on a cluster\-by\-cluster basis\. For more information, see [Use IAM Policies to Allow and Deny User Permissions](emr-plan-access-iam.md)\.
+ **IAM roles for EMRFS requests to Amazon S3**

  You can control whether cluster users can access files from within Amazon EMR based on user, group, or the location of EMRFS data in Amazon S3\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.
+ **IAM roles**

  The Amazon EMR service role, instance profile, and service\-linked role control how Amazon EMR is able to access other AWS services\. For more information, see [Configure IAM Roles for Amazon EMR Permissions to AWS Services](emr-iam-roles.md)\.
+ **Kerberos**

  You can set up Kerberos to provide strong authentication through secret\-key cryptography\. For more information, see [Use Kerberos Authentication](emr-kerberos.md)\.
+ **Secure Socket Shell \(SSH\)**

  SSH provides a secure way for users to connect to the command line on cluster instances\. It also provides tunneling to view web interfaces that applications host on the master node\. Clients can authenticate using Kerberos or an Amazon EC2 key pair\. For more information, see [Use an Amazon EC2 Key Pair for SSH Credentials](emr-plan-access-ssh.md) and [Connect to the Cluster](emr-connect-master-node.md)\.
+ **Data encryption**

  You can implement data encryption to help protect data at rest in Amazon S3 and in cluster instance storage, and data in transit\. For more information, see [Encrypt Data in Transit and At Rest](emr-data-encryption.md)\. You can also use a custom Amazon Linux AMI to encrypt the EBS root device volume of cluster instances\. For more information, see [Using a Custom AMI](emr-custom-ami.md)\.
+ **Security groups**

  Security groups act as a virtual firewall for EMR cluster instances, limiting inbound and outbound network traffic\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.
+ **Security configurations**

  Security configurations in Amazon EMR are templates for a security setup\. You can create a security configuration to conveniently re\-use a security setup whenever you create a cluster\. For more information, see [Use Security Configurations to Set Up Cluster Security](emr-security-configurations.md)\.

When an Amazon EC2 instance in a cluster that is based on the default Amazon Linux AMI for Amazon EMR boots for the first time, Critical security updates are installed by default\. Other updates are not installed\. Depending on the security posture of your application and the length of time that a cluster runs, you may choose to periodically reboot your cluster to apply security updates, or create a bootstrap action to customize package installation and updates\. You may also choose to test and then install select security updates on running cluster instances\. For more information, see [Working with Amazon Linux AMIs in Amazon EMR](emr-ami.md)\.