# Use EMR File System \(EMRFS\)<a name="emr-fs"></a>

The EMR File System \(EMRFS\) is an implementation of HDFS that all Amazon EMR clusters use for reading and writing regular files from Amazon EMR directly to Amazon S3\. EMRFS provides the convenience of storing persistent data in Amazon S3 for use with Hadoop while also providing features like consistent view and data encryption\. 

Consistent view provides consistency checking for list and read\-after\-write \(for new put requests\) for objects in Amazon S3\. Data encryption allows you to encrypt objects that EMRFS writes to Amazon S3, and enables EMRFS to work with encrypted objects in Amazon S3\. If you are using Amazon EMR release version 4\.8\.0 or later, you can use security configurations to set up encryption for EMRFS objects in Amazon S3, along with other encryption settings\. For more information, see [Encryption Options](emr-data-encryption-options.md)\. If you use an earlier release version of Amazon EMR, you can manually configure encryption settings\. For more information, see [Specifying Amazon S3 Encryption Using EMRFS Properties](emr-emrfs-encryption.md)\.

When using Amazon EMR release version 5\.10\.0 or later, you can use different IAM roles for EMRFS requests to Amazon S3 based on cluster users, groups, or the location of EMRFS data in Amazon S3\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.

**Topics**
+ [Consistent View](emr-plan-consistent-view.md)
+ [Authorizing Access to EMRFS Data in Amazon S3](emr-plan-credentialsprovider.md)
+ [Specifying Amazon S3 Encryption Using EMRFS Properties](emr-emrfs-encryption.md)