# Encryption Options<a name="emr-data-encryption-options"></a>

Beginning with Amazon EMR release version 4\.8\.0, you can use a security configuration to specify settings for encrypting data at\-rest, data in\-transit, or both\. Each security configuration that you create is stored in Amazon EMR rather than in the cluster configuration, so you can easily reuse a configuration to specify data encryption settings whenever you create a cluster\. For more information, see [Create a Security Configuration](emr-create-security-configuration.md)\.

The following diagram shows the different data encryption options available with security configurations\. When you enable at\-rest data encryption, you specify options for encrypting EMRFS data in Amazon S3 and local disk encryption together\. You can choose to enable only at\-rest encryption, only in\-transit encryption, or both\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-encryption.png)

The following encryption options are also available and are not configured using a security configuration:
+ Using a security configuration does not encrypt the EBS root device volume on cluster instances\. Beginning with Amazon EMR release version 5\.7\.0, you can do this by specifying a custom AMI with an encrypted EBS root device volume\. For more information, see [Using a Custom AMI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html) in the *Amazon EMR Management Guide*\.
+ If you are using a release version of Amazon EMR that does not support security configurations, you can configure encryption for EMRFS data in Amazon S3 manually\. For more information, see [Specifying Amazon S3 Encryption Using EMRFS Properties](emr-emrfs-encryption.md)\.
+ Optionally, beginning with Amazon EMR release version 4\.1\.0 and later, you can choose to configure transparent encryption in HDFS\. For more information, see [Transparent Encryption in HDFS on Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hdfs-config.html#emr-encryption-tdehdfs) in the *Amazon EMR Release Guide*\.

Data encryption requires keys and certificates\. A security configuration gives you the flexibility to choose from several options, including keys managed by AWS Key Management Service, keys managed by Amazon S3, and keys and certificates from custom providers that you supply\. When using AWS KMS as your key provider, charges apply for the storage and use of encryption keys\. For more information, see [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/)\.

Before you specify encryption options, decide on the key and certificate management systems you want to use, so you can first create the keys and certificates or the custom providers that you specify as part of encryption settings\.

## At\-Rest Encryption for EMRFS data in Amazon S3<a name="emr-encryption-s3"></a>

Amazon S3 encryption works with EMR File System \(EMRFS\) objects read from and written to Amazon S3\. You specify Amazon S3 server\-side encryption \(SSE\) or client\-side encryption \(CSE\) when you enable at\-rest encryption\. Amazon S3 SSE and CSE encryption with EMRFS are mutually exclusive; you can choose either but not both\. Regardless of whether Amazon S3 encryption is enabled, Transport Layer Security \(TLS\) encrypts the EMRFS objects in\-transit between EMR cluster nodes and Amazon S3\. For in\-depth information about Amazon S3 encryption, see [Protecting Data Using Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html) in the *Amazon Simple Storage Service Developer Guide*\. 

### Amazon S3 Server\-Side Encryption<a name="emr-encryption-s3-sse"></a>

When you set up Amazon S3 server\-side encryption, Amazon S3 encrypts data at the object level as it writes the data to disk and decrypts the data when it is accessed\. For more information about SSE, see [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon Simple Storage Service Developer Guide*\.

You can choose between two different key management systems when you specify SSE in Amazon EMR: 
+ **SSE\-S3**: Amazon S3 manages keys for you\.
+ **SSE\-KMS**: You use an AWS KMS customer master key \(CMK\) set up with policies suitable for Amazon EMR\. For more information about key requirements for Amazon EMR, see [Using AWS KMS Customer Master Keys \(CMKs\) for Encryption](emr-encryption-enable.md#emr-awskms-keys)\. When you use AWS KMS, charges apply for the storage and use of encryption keys\. For more information, see [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/)\.

SSE with customer\-provided keys \(SSE\-C\) is not available for use with Amazon EMR\.

### Amazon S3 Client\-Side Encryption<a name="emr-encryption-s3-cse"></a>

With Amazon S3 client\-side encryption, the Amazon S3 encryption and decryption takes place in the EMRFS client on your cluster\. Objects are encrypted before being uploaded to Amazon S3 and decrypted after they are downloaded\. The provider you specify supplies the encryption key that the client uses\. The client can use keys provided by AWS KMS \(CSE\-KMS\) or a custom Java class that provides the client\-side master key \(CSE\-C\)\. The encryption specifics are slightly different between CSE\-KMS and CSE\-C, depending on the specified provider and the metadata of the object being decrypted or encrypted\. For more information about these differences, see [Protecting Data Using Client\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html) in the *Amazon Simple Storage Service Developer Guide*\.

**Note**  
Amazon S3 CSE only ensures that EMRFS data exchanged with Amazon S3 is encrypted; not all data on cluster instance volumes is encrypted\. Furthermore, because Hue does not use EMRFS, objects that the Hue S3 File Browser writes to Amazon S3 are not encrypted\.

## At\-rest Encryption for Local Disks<a name="emr-encryption-localdisk"></a>

Local disk encryption within a security configuration applies to instance store and EBS storage volumes in a cluster\. It does not apply to EBS root device volumes\. Beginning with Amazon EMR version 5\.7\.0, you can specify a custom AMI to encrypt the EBS root device volumes of EC2 instances\. This is a separate setting from security configurations\. For more information, see [Using a Custom AMI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html) in the *Amazon EMR Management Guide*\. 

Two mechanisms work together to encrypt storage volumes when you enable at\-rest data encryption:
+ **Open\-source HDFS Encryption**: HDFS exchanges data between cluster instances during distributed processing, and also reads from and writes data to instance store volumes and the EBS volumes attached to instances\. The following open\-source Hadoop encryption options are activated when you enable local\-disk encryption:
  + [Secure Hadoop RPC](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SecureMode.html#Data_Encryption_on_RPC) is set to "Privacy", which uses Simple Authentication Security Layer \(SASL\)\. 
  + [Data encryption on HDFS block data transfer](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SecureMode.html#Data_Encryption_on_Block_data_transfer.) is set to true and is configured to use AES 256 encryption\.
**Note**  
You can activate additional Apache Hadoop encryption by enabling in\-transit encryption \(see [In\-Transit Data Encryption](#emr-encryption-intransit)\)\. These encryption settings do not activate HDFS transparent encryption, which you can configure manually\. For more information, see [Transparent Encryption in HDFS on Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-encryption-tdehdfs.html) in the *Amazon EMR Release Guide*\.
+ **LUKS**\. In addition to HDFS encryption, the Amazon EC2 instance store volumes and the attached Amazon EBS volumes of cluster instances are encrypted using LUKS\. For more information about LUKS encryption, see the [LUKS on\-disk specification](https://gitlab.com/cryptsetup/cryptsetup/wikis/Specification)\. At\-rest encryption does not encrypt the EBS root device volume \(boot volume\)\. To encrypt the EBS root device volume, use Amazon EMR version 5\.7\.0 or later and specify a custom AMI\. For more information, see [Customizing an AMI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html) in the *Amazon EMR Management Guide*\.

  For your key provider, you can use an AWS KMS CMK set up with policies suitable for Amazon EMR, or a custom Java class that provides the encryption artifacts\. When you use AWS KMS, charges apply for the storage and use of encryption keys\. For more information, see [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/)\.

## In\-Transit Data Encryption<a name="emr-encryption-intransit"></a>

Several encryption mechanisms are enabled with in\-transit encryption\. These are open\-source features, are application\-specific, and may vary by Amazon EMR release\. The following application\-specific encryption features can be enabled using security configurations:
+ Hadoop \(for more information, see [Hadoop in Secure Mode](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SecureMode.html) in Apache Hadoop documentation\): 
  + [Hadoop MapReduce Encrypted Shuffle](https://hadoop.apache.org/docs/r2.7.1/hadoop-mapreduce-client/hadoop-mapreduce-client-core/EncryptedShuffle.html) uses TLS\.
  + [Secure Hadoop RPC](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SecureMode.html#Data_Encryption_on_RPC) is set to "Privacy" and uses SASL \(activated in Amazon EMR when at\-rest encryption is enabled\)\.
  + [Data encryption on HDFS block data transfer](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SecureMode.html#Data_Encryption_on_Block_data_transfer.) uses AES 256 \(activated in Amazon EMR when at\-rest encryption is enabled in the security configuration\)\. 
+ HBase:
  + When Kerberos is enabled, the `hbase.rpc.protection` property is set to `privacy` for encrypted communication\. For more information, see [Client\-side Configuration for Secure Operation](http://hbase.apache.org/0.94/book/security.html#d1984e4501) in Apache HBase documentation\. For more information about Kerberos with Amazon EMR, see [Use Kerberos Authentication](emr-kerberos.md)\.
+ Presto:
  + Internal communication between Presto nodes uses SSL/TLS \(Amazon EMR version 5\.6\.0 and later only\)\.
+ Tez:
  + [Tez Shuffle Handler](https://tez.apache.org/releases/0.8.4/tez-runtime-library-javadocs/configs/TezRuntimeConfiguration.html) uses TLS \(`tez.runtime.ssl.enable`\)\.
+ Spark \(for more information, see [Spark security settings](http://spark.apache.org/docs/latest/security.html)\):
  + Internal RPC communication between Spark components—for example, the block transfer service and the external shuffle service—is encrypted using the AES\-256 cipher in Amazon EMR release version 5\.9\.0 and later\. In earlier releases, internal RPC communication is encrypted using SASL with DIGEST\-MD5 as the cipher\.
  + HTTP protocol communication with user interfaces such as Spark History Server and HTTPS\-enabled file servers is encrypted using Spark's SSL configuration\. For more information, see [SSL Configuration](https://spark.apache.org/docs/latest/security.html#ssl-configuration) in Spark documentation\.

You specify the encryption artifacts used for in\-transit encryption in one of two ways: either by providing a zipped file of certificates that you upload to Amazon S3, or by referencing a custom Java class that provides encryption artifacts\. For more information, see [Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption](emr-encryption-enable.md#emr-encryption-certificates)\.