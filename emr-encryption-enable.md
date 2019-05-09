# Create Keys and Certificates for Data Encryption<a name="emr-encryption-enable"></a>

Before you specify encryption options using a security configuration, decide on the provider you want to use for keys and encryption artifacts \(for example, AWS KMS or a custom provider that you create\) and create the keys or key provider as described in this section\.

## Providing Keys for At\-Rest Data Encryption with Amazon EMR<a name="emr-encryption-create-keys"></a>

You can use AWS Key Management Service \(AWS KMS\) or a custom key provider for at\-rest data encryption in Amazon EMR\. When you use AWS KMS, charges apply for the storage and use of encryption keys\. For more information, see [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/)\. 

This topic provides key policy details for an AWS KMS CMK to be used with Amazon EMR, as well as guidelines and code examples for writing a custom key provider class for Amazon S3 encryption\. For more information about creating keys, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

### Using AWS KMS Customer Master Keys \(CMKs\) for Encryption<a name="emr-awskms-keys"></a>

The AWS KMS encryption key must be created in the same region as your Amazon EMR cluster instance and the Amazon S3 buckets used with EMRFS\. If the key that you specify is in a different account from the one that you use to configure a cluster, you must specify the key using its ARN\.

The role for the Amazon EC2 instance profile must have permissions to use the CMK you specify\. The default role for the instance profile in Amazon EMR is `EMR_EC2_DefaultRole`\. If you use a different role for the instance profile, or you use IAM roles for EMRFS requests to Amazon S3, make sure that each role is added as a key user as appropriate\. This gives the role permissions to use the CMK\. For more information, see [Using Key Policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users) in the *AWS Key Management Service Developer Guide* and [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)\. Although you may use the same AWS KMS customer master key \(CMK\) for Amazon S3 data encryption as you use for local disk encryption, using separate keys is recommended\. 

You can use the AWS Management Console to add your instance profile or EC2 instance profile to the list of key users for the specified AWS KMS CMK, or you can use the AWS CLI or an AWS SDK to attach an appropriate key policy\.

The procedure below describes how to add the default EMR instance profile, `EMR_EC2_DefaultRole` as a *key user* using the AWS Management Console\. It assumes that you have already created a CMK\. To create a new CMK, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

**To add the EC2 instance profile for Amazon EMR to the list of encryption key users**

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Select the alias of the CMK to modify\.

1. On the key details page under **Key Users**, choose **Add**\.

1. In the **Attach** dialog box, select the appropriate role\. The name of the default role is `EMR_EC2_DefaultRole`\.

1. Choose **Attach**\.

### Creating a Custom Key Provider<a name="emr-custom-keys"></a>

When using a security configuration, you must specify a different provider class name for local disk encryption and Amazon S3 encryption\.

When you create a custom key provider, the application is expected to implement the [EncryptionMaterialsProvider interface](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/elasticmapreduce/spi/security/EncryptionMaterialsProvider.html), which is available in the AWS SDK for Java version 1\.11\.0 and later\. The implementation can use any strategy to provide encryption materials\. You may, for example, choose to provide static encryption materials or integrate with a more complex key management system\.

The encryption algorithm used for custom encryption materials must be **AES/GCM/NoPadding**\.

The EncryptionMaterialsProvider class gets encryption materials by encryption context\. Amazon EMR populates encryption context information at runtime to help the caller determine the correct encryption materials to return\.

**Example Example: Using a Custom Key Provider for Amazon S3 Encryption with EMRFS**  
When Amazon EMR fetches the encryption materials from the EncryptionMaterialsProvider class to perform encryption, EMRFS optionally populates the materialsDescription argument with two fields: the Amazon S3 URI for the object and the JobFlowId of the cluster, which can be used by the EncryptionMaterialsProvider class to return encryption materials selectively\.  
For example, the provider may return different keys for different Amazon S3 URI prefixes\. It is the description of the returned encryption materials that is eventually stored with the Amazon S3 object rather than the materialsDescription value that is generated by EMRFS and passed to the provider\. While decrypting an Amazon S3 object, the encryption materials description is passed to the EncryptionMaterialsProvider class, so that it can, again, selectively return the matching key to decrypt the object\.  
An EncryptionMaterialsProvider reference implementation is provided below\. Another custom provider, [EMRFSRSAEncryptionMaterialsProvider](https://github.com/awslabs/emr-sample-apps/tree/master/emrfs-plugins/EMRFSRSAEncryptionMaterialsProvider), is available from GitHub\.   

```
import com.amazonaws.services.s3.model.EncryptionMaterials;
import com.amazonaws.services.s3.model.EncryptionMaterialsProvider;
import com.amazonaws.services.s3.model.KMSEncryptionMaterials;
import org.apache.hadoop.conf.Configurable;
import org.apache.hadoop.conf.Configuration;

import java.util.Map;

/**
 * Provides KMSEncryptionMaterials according to Configuration
 */
public class MyEncryptionMaterialsProviders implements EncryptionMaterialsProvider, Configurable{
  private Configuration conf;
  private String kmsKeyId;
  private EncryptionMaterials encryptionMaterials;

  private void init() {
    this.kmsKeyId = conf.get("my.kms.key.id");
    this.encryptionMaterials = new KMSEncryptionMaterials(kmsKeyId);
  }

  @Override
  public void setConf(Configuration conf) {
    this.conf = conf;
    init();
  }

  @Override
  public Configuration getConf() {
    return this.conf;
  }

  @Override
  public void refresh() {

  }

  @Override
  public EncryptionMaterials getEncryptionMaterials(Map<String, String> materialsDescription) {
    return this.encryptionMaterials;
  }

  @Override
  public EncryptionMaterials getEncryptionMaterials() {
    return this.encryptionMaterials;
  }
}
```

## Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption<a name="emr-encryption-certificates"></a>

For in\-transit data encryption using a security configuration \(available in Amazon EMR release version 4\.8\.0 or later\), you have two options to specify encryption artifacts:
+ You can manually create PEM certificates, include them in a zip file, and then reference the zip file in Amazon S3\.
+ You can implement a custom certificate provider as a Java class\. You specify the JAR file of the application in Amazon S3, and then provide the full class name of the provider as declared in the application\. The class must implement the [TLSArtifactsProvider](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/elasticmapreduce/spi/security/TLSArtifactsProvider.html) interface available beginning with the AWS SDK for Java version 1\.11\.0\.

Amazon EMR automatically downloads artifacts to each node in the cluster and later uses them to implement the open\-source, in\-transit encryption features\. For more information about available options, see [In\-Transit Data Encryption](emr-data-encryption-options.md#emr-encryption-intransit)\.

### Using PEM Certificates<a name="emr-encryption-pem-certificate"></a>

When you specify a zip file for in\-transit encryption, the security configuration expects PEM files within the zip file to be named exactly as they appear below:


**In\-transit encryption certificates**  

| File name | Required/optional | Details | 
| --- | --- | --- | 
| privateKey\.pem | Required | Private key | 
| certificateChain\.pem | Required | Certificate chain | 
| trustedCertificates\.pem | Optional | Required if the provided certificate is not signed by either the Java default trusted root certification authority \(CA\) or an intermediate CA that can link to the Java default trusted root CA\. The Java default trusted root CAs can be found in jre/lib/security/cacerts\. | 

You likely want to configure the private key PEM file to be a wildcard certificate that enables access to the Amazon VPC domain in which your cluster instances reside\. For example, if your cluster resides in us\-east\-1 \(N\. Virginia\), you could specify a common name in the certificate configuration that allows access to the cluster by specifying `CN=*.ec2.internal` in the certificate subject definition\. If your cluster resides in us\-west\-2 \(Oregon\), you could specify `CN=*.us-west-2.compute.internal`\. For more information about EMR cluster configuration within Amazon VPC, see [Select an Amazon VPC Subnet for the Cluster](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-vpc-subnet.html)\. 

The following example demonstrates how to use [OpenSSL](https://www.openssl.org/) to generate a self\-signed X\.509 certificate with a 1024\-bit RSA private key\. The key allows access to the issuer's Amazon EMR cluster instances in the us\-west\-2 \(Oregon\) region as specified by the `*.us-west-2.compute.internal` domain name as the common name\.

Other optional subject items—such as country \(C\), state \(S\), Locale \(L\), etc\.—are specified\. Because a self\-signed certificate is generated, the second command in the example copies the `certificateChain.pem` file to the `trustedCertificates.pem` file\. The third command uses `zip` to create the `my-certs.zip` file that contains the certificates\.

**Important**  
This example is a proof\-of\-concept demonstration only\. Using self\-signed certificates is not recommended and presents a potential security risk\. For production systems, use a trusted certification authority \(CA\) to issue certificates\.

```
$ openssl req -x509 -newkey rsa:1024 -keyout privateKey.pem -out certificateChain.pem -days 365 -nodes -subj '/C=US/ST=Washington/L=Seattle/O=MyOrg/OU=MyDept/CN=*.us-west-2.compute.internal'
$ cp certificateChain.pem trustedCertificates.pem
$ zip -r -X my-certs.zip certificateChain.pem privateKey.pem trustedCertificates.pem
```