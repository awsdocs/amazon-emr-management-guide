# Encrypt Data in Transit and At Rest<a name="emr-data-encryption"></a>

Data encryption helps prevent unauthorized users from reading data on a cluster and associated data storage systems\. This includes data saved to persistent media, known as data *at\-rest*, and data that may be intercepted as it travels the network, known as data *in\-transit*\.

Beginning with Amazon EMR version 4\.8\.0, you can use Amazon EMR security configurations to configure data encryption settings for clusters more easily\. Security configurations offer settings to enable security for data in\-transit and data at\-rest in Amazon Elastic Block Store \(Amazon EBS\) storage volumes and EMRFS on Amazon S3\. 

Optionally, beginning with Amazon EMR release version 4\.1\.0 and later, you can choose to configure transparent encryption in HDFS\. For more information, see [Transparent Encryption in HDFS on Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hdfs-config.html#emr-encryption-tdehdfs) in the *Amazon EMR Release Guide*\. Beginning with Amazon EMR release version 5\.7\.0, you can specify a custom AMI with an encrypted EBS root device volume\. For more information, see [Using a Custom AMI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html)\. These features are not configured using security configurations\.

**Topics**
+ [Encryption Options](emr-data-encryption-options.md)
+ [Create Keys and Certificates for Data Encryption](emr-encryption-enable.md)