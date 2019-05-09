# Using the Default Amazon Linux AMI for Amazon EMR<a name="emr-default-ami"></a>

Each Amazon EMR release version uses a default Amazon Linux AMI for Amazon EMR unless you specify a custom AMI\. The default AMI is based on the most up\-to\-date Amazon Linux AMI available at the time of the Amazon EMR release\. The AMI is tested for compatibility with the big\-data applications and Amazon EMR features included with that release version\.

Each Amazon EMR release version is "locked" to the Amazon Linux AMI version to maintain compatibility\. This means that the same Amazon Linux AMI version is used for an Amazon EMR release version even when newer Amazon Linux AMIs become available\. For this reason, we recommend that you use the latest Amazon EMR release version \(currently 5\.23\.0\) unless you need an earlier version for compatibility and are unable to migrate\.

If you must use an earlier release version of Amazon EMR for compatibility, we recommend that you use the latest release in a series\. For example, if you must use the 5\.12 series, use 5\.12\.2 instead of 5\.12\.0 or 5\.12\.1\. If a new release becomes available in a series, consider migrating your applications to the new release\.

## How Software Updates Are Managed<a name="emr-default-ami-managing"></a>

When an Amazon EC2 instance in a cluster that is based on the default Amazon Linux AMI for Amazon EMR boots for the first time, it checks the enabled package repositories for Amazon Linux and Amazon EMR for software updates that apply to the AMI version\. As with other Amazon EC2 instances, critical and important security updates from these repositories are installed automatically\. For more information, see [Package Repository](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/amazon-linux-ami-basics.html#package-repository) in the *Amazon EC2 User Guide for Linux Instances*\. Other software packages and kernel updates are not installed because that risks introducing compatibility errors\.

When you connect to a cluster instance using SSH, the first few lines of screen output provide a link to the release notes for the Amazon Linux AMI that the instance uses, a notice of the most recent Amazon Linux AMI version, a notice of the number of packages available for update from the enabled repositories, and a directive to run `sudo yum update`\.

**Important**  
We strongly recommend that you do not run `sudo yum update` on cluster instances, either while connected using SSH or using a bootstrap action\. This might cause incompatibilities because all packages are installed indiscriminately\.

**Best Practices for Managing Software Updates**
+ If you use an earlier release version of Amazon EMR, consider and test a migration to the latest release before updating software packages\.
+ If you migrate to a later release version or you upgrade software packages, test the implementation in a non\-production environment first\. The option to clone clusters using the Amazon EMR management console is helpful for this\.
+ Evaluate software updates for your applications and for your version of Amazon Linux AMI on an individual basis\. Only test and install packages in production environments that you determine to be absolutely necessary for your security posture, application functionality, or performance\.
+ Watch the [Amazon Linux Security Center](https://alas.aws.amazon.com/) for updates\.
+ Avoid installing packages by connecting to individual cluster instances using SSH\. Instead, use a bootstrap action to install and update packages on all cluster instances as necessary\. This requires that you terminate a cluster and relaunch it\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\.