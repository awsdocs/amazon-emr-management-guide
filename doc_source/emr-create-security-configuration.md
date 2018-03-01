# Create a Security Configuration<a name="emr-create-security-configuration"></a>

**To create a security configuration using the console**

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. In the navigation pane, choose **Security Configurations**, **Create security configuration**\. 

1. Type a **Name** for the security configuration\.

1. Choose options for **Encryption** and **Authentication** as described in the sections below and then choose **Create**\.

**To create a security configuration using the AWS CLI**

1. Use the `create-security-configuration` command with the following syntax:

   ```
   aws emr create-security-configuration --name "SecConfigName" --security-configuration SecConfigDef
   ```

   For *SecConfigName*, specify the name of the security configuration\. This is the name you specify when you create a cluster that uses this security configuration\.

1. For `SecConfigDef`, specify an inline JSON structure or the path to a JSON file in Amazon S3, such as `s3://mybucket/MySecConfig.json` or a local file, such as `file://MySecConfig.json`\. The JSON parameters define options for **Encryption** and **Authentication** as described in the sections below\.

## Configure Data Encryption<a name="emr-security-configuration-encryption"></a>

Before you configure encryption in a security configuration, create the keys and certificates that are used for encryption\. For more information, see [Providing Keys for At\-Rest Data Encryption with Amazon EMR](emr-encryption-enable.md#emr-encryption-create-keys) and [Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption](emr-encryption-enable.md#emr-encryption-certificates)\.

When you create a security configuration, you specify two sets of encryption options: at\-rest data encryption and in\-transit data encryption\. Options for at\-rest data encryption include both Amazon S3 with EMRFS and local\-disk encryption\. In\-transit encryption options enable the open\-source encryption features for certain applications that support Transport Layer Security \(TLS\)\. At\-rest options and in\-transit options can be enabled together or separately\. For more information, see [Encrypt Data in Transit and At Rest](emr-data-encryption.md)\.

### Specifying Encryption Options Using the Console<a name="emr-security-configuration-encryption-console"></a>

Choose options under **Encryption** according to the following guidelines\.

+ Choose **At rest encryption** to encrypt data stored within the file system\. This also enables Hadoop Distributed File System \(HDFS\) block\-transfer encryption and RPC encryption, which need no further configuration\.

+ Under **S3 data encryption**, for **Encryption mode**, choose a value to determines how Amazon EMR encrypts Amazon S3 data with EMRFS\. 

  What you do next depends on the encryption mode you chose:

  + **SSE\-S3**

    Specifies [Server\-side encryption with Amazon S3\-managed encryption keys](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html)\. You don't need to do anything more because Amazon S3 handles keys for you\.

  + **SSE\-KMS** or **CSE\-KMS**

    Specifies [server\-side encryption with AWS KMS\-managed keys \(SSE\-KMS\)](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html) or [client\-side encryption with AWS KMS\-managed keys \(CSE\-KMS\)](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryption.html)\. For **AWS KMS Key**, select a key\. The key must exist in the same region as your Amazon EMR cluster\. For key requirements, see [Using AWS KMS Customer Master Keys \(CMKs\) for Encryption](emr-encryption-enable.md#emr-awskms-keys)\.

  + **CSE\-Custom**

    Specifies [client\-side encryption using a custom client\-side master key \(CSE\-Custom\)](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingClientSideEncryptionUpload.html)\. For **S3 object**, enter the location in Amazon S3, or the Amazon S3 ARN, of your custom key\-provider JAR file\. Then, for **Key provider class**, enter the full class name of a class declared in your application that implements the EncryptionMaterialsProvider interface\.

+ Under **Local disk encryption**, choose a value for **Key provider type**\. Amazon EMR uses this key for Linux Unified Key System \(LUKS\) encryption for the local volumes \(except boot volumes\) attached to your cluster nodes\.

  + **AWS KMS**

    Select this option to specify an AWS KMS customer master key \(CMK\)\. For **AWS KMS Key**, select a key\. The key must exist in the same region as your Amazon EMR cluster\. For more information about key requirements, see [Using AWS KMS Customer Master Keys \(CMKs\) for Encryption](emr-encryption-enable.md#emr-awskms-keys)\.

  + **Custom**

    Select this option to specify a custom key provider\. For **S3 object**, enter the location in Amazon S3, or the Amazon S3 ARN, of your custom key\-provider JAR file\. For **Key provider class**, enter the full class name of a class declared in your application that implements the EncryptionMaterialsProvider interface\. The class name you provide here must be different from the class name provided for CSE\-Custom\.

+ Choose **In\-transit encryption** to enable the open\-source TLS encryption features for in\-transit data\. Choose a **Certificate provider type** according to the following guidelines: 

  + **PEM**

    Select this option to use PEM files that you provide within a zip file\. Two artifacts are required within the zip file: privateKey\.pem and certificateChain\.pem\. A third file, trustedCertificates\.pem, is optional\. See [Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption](emr-encryption-enable.md#emr-encryption-certificates) for details\. For **S3 object**, specify the location in Amazon S3, or the Amazon S3 ARN, of the zip file field\. 

  + **Custom**

    Select this option to specify a custom certificate provider and then, for **S3 object**, enter the location in Amazon S3, or the Amazon S3 ARN, of your custom certificate\-provider JAR file\. For **Key provider class**, enter the full class name of a class declared in your application that implements the TLSArtifactsProvider interface\. 

### Specifying Encryption Options Using the AWS CLI<a name="emr-security-configuration-encryption-cli"></a>

The sections that follow use sample scenarios to illustrate well\-formed \-\-security\-configuration JSON for different configurations and key providers, followed by a reference of the JSON parameters and appropriate values\.

#### Example In\-Transit Data Encryption Options<a name="emr-encryption-intransit-cli"></a>

The example below illustrates the following scenario:

+ In\-transit data encryption is enabled and at\-rest data encryption is disabled\.

+ A zip file with certificates in Amazon S3 is used as the key provider \(see [Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption](emr-encryption-enable.md#emr-encryption-certificates) for certificate requirements\.

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : true,
		"EnableAtRestEncryption" : false,
		"InTransitEncryptionConfiguration" : {
			"TLSCertificateConfiguration" : {
				"CertificateProviderType" : "PEM",
				"S3Object" : "s3://MyConfigStore/artifacts/MyCerts.zip"
			}
		}
	}
}'
```

The example below illustrates the following scenario:

+ In\-transit data encryption is enabled and at\-rest data encryption is disabled\.

+ A custom key provider is used \(see [Providing Certificates for In\-Transit Data Encryption with Amazon EMR Encryption](emr-encryption-enable.md#emr-encryption-certificates) for certificate requirements\)\.

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : true,
		"EnableAtRestEncryption" : false,
		"InTransitEncryptionConfiguration" : {
			"TLSCertificateConfiguration" : {
				"CertificateProviderType" : "Custom",
				"S3Object" : "s3://MyConfig/artifacts/MyCerts.jar",
				"CertificateProviderClass" : "com.mycompany.MyCertProvider"
			}
		}
 	}
}'
```

#### Example At\-Rest Data Encryption Options<a name="emr-encryption-atrest-cli"></a>

The example below illustrates the following scenario:

+ In\-transit data encryption is disabled and at\-rest data encryption is enabled

+ SSE\-S3 is used for Amazon S3 encryption

+ Local disk encryption uses AWS KMS as the key provider

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : false,
		"EnableAtRestEncryption" : true,
		"AtRestEncryptionConfiguration" : {
			"S3EncryptionConfiguration" : {
				"EncryptionMode" : "SSE-S3"
			},
			"LocalDiskEncryptionConfiguration" : {
				"EncryptionKeyProviderType" : "AwsKms",
				"AwsKmsKey" : "arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"
			}
		}
 	}
}'
```

The example below illustrates the following scenario:

+ In\-transit data encryption is enabled and references a zip file with PEM certificates in Amazon S3, using the ARN

+ SSE\-KMS is used for Amazon S3 encryption

+ Local disk encryption uses AWS KMS as the key provider

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : true,
		"EnableAtRestEncryption" : true,
		"InTransitEncryptionConfiguration" : {
			"TLSCertificateConfiguration" : {
				"CertificateProviderType" : "PEM",
				"S3Object" : "arn:aws:s3:::MyConfigStore/artifacts/MyCerts.zip"
			}
		},
		"AtRestEncryptionConfiguration" : {
			"S3EncryptionConfiguration" : {
				"EncryptionMode" : "SSE-KMS",
				"AwsKmsKey" : "arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"
			},
			"LocalDiskEncryptionConfiguration" : {
				"EncryptionKeyProviderType" : "AwsKms",
				"AwsKmsKey" : "arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"
			}
		}
	}
}'
```

The example below illustrates the following scenario:

+ In\-transit data encryption is enabled and references a zip file with PEM certificates in Amazon S3

+ CSE\-KMS is used for Amazon S3 encryption

+ Local disk encryption uses a custom key provider referenced by its ARN

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : true,
		"EnableAtRestEncryption" : true,
		"InTransitEncryptionConfiguration" : {
			"TLSCertificateConfiguration" : {
				"CertificateProviderType" : "PEM",
				"S3Object" : "s3://MyConfigStore/artifacts/MyCerts.zip"
			}
		},
		"AtRestEncryptionConfiguration" : {
			"S3EncryptionConfiguration" : {
				"EncryptionMode" : "CSE-KMS",
				"AwsKmsKey" : "arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"
			},
			"LocalDiskEncryptionConfiguration" : {
				"EncryptionKeyProviderType" : "Custom",
				"S3Object" : "arn:aws:s3:::artifacts/MyKeyProvider.jar",
				"EncryptionKeyProviderClass" : "com.mycompany.MyKeyProvider.jar"
			}
		}
	}
}'
```

The example below illustrates the following scenario:

+ In\-transit data encryption is enabled with a custom key provider

+ CSE\-Custom is used for Amazon S3 data

+ Local disk encryption uses a custom key provider

```
aws emr create-security-configuration --name "MySecConfig" --security-configuration '{
	"EncryptionConfiguration": {
		"EnableInTransitEncryption" : "true",
		"EnableAtRestEncryption" : "true",
		"InTransitEncryptionConfiguration" : {
			"TLSCertificateConfiguration" : {
				"CertificateProviderType" : "Custom",
				"S3Object" : "s3://MyConfig/artifacts/MyCerts.jar", 
				"CertificateProviderClass" : "com.mycompany.MyCertProvider"
			}
		},
		"AtRestEncryptionConfiguration" : {
			"S3EncryptionConfiguration" : {
				"EncryptionMode" : "CSE-Custom",
				"S3Object" : "s3://MyConfig/artifacts/MyCerts.jar", 
				"EncryptionKeyProviderClass" : "com.mycompany.MyKeyProvider"
				},
			"LocalDiskEncryptionConfiguration" : {
				"EncryptionKeyProviderType" : "Custom",
				"S3Object" : "s3://MyConfig/artifacts/MyCerts.jar",
				"EncryptionKeyProviderClass" : "com.mycompany.MyKeyProvider"
			}
		}
	}
}'
```

#### JSON Reference for Encryption Settings<a name="emr-encryption-cli-parameters"></a>

The following table lists the JSON parameters for encryption settings and provides a description of acceptable values for each parameter\.


| Parameter | Description | 
| --- | --- | 
| "EnableInTransitEncryption" : true | false | Specify true to enable in\-transit encryption and false to disable it\. If omitted, false is assumed, and in\-transit encryption is disabled\. | 
| "EnableAtRestEncryption" : true | false | Specify true to enable at\-rest encryption and false to disable it\. If omitted, false is assumed and at\-rest encryption is disabled\. | 
| In\-transit encryption parameters | 
| "InTransitEncryptionConfiguration" : | Specifies a collection of values used to configure in\-transit encryption when EnableInTransitEncryption is true\. | 
|  "CertificateProviderType" : "PEM" | "Custom" | Specifies whether to use PEM certificates referenced with a zipped file, or a Custom certificate provider\. If PEM is specified, S3Object must be a reference to the location in Amazon S3 of a zip file containing the certificates\. If Custom is specified, S3Object must be a reference to the location in Amazon S3 of a JAR file, followed by a CertificateProviderClass entry\. | 
|  "S3Object" : "ZipLocation" | "JarLocation" | Provides the location in Amazon S3 to a zip file when PEM is specified, or to a JAR file when Custom is specified\. The format can be a path \(for example, s3://MyConfig/articfacts/CertFiles\.zip\) or an ARN \(for example, arn:aws:s3:::Code/MyCertProvider\.jar\)\. If a zip file is specified, it must contain files named exactly privateKey\.pem and certificateChain\.pem\. A file named trustedCertificates\.pem is optional\. | 
|   "CertificateProviderClass" : "MyClassID" | Required only if Custom is specified for CertificateProviderType\. MyClassID specifies a full class name declared in the JAR file, which implements the TLSArtifactsProvider interface\. For example, com\.mycompany\.MyCertProvider\. | 
| At\-rest encryption parameters | 
| "AtRestEncryptionConfiguration" :  | Specifies a collection of values for at\-rest encryption when EnableAtRestEncryption is true, including Amazon S3 encryption and local disk encryption\. | 
| Amazon S3 encryption parameters | 
| "S3EncryptionConfiguration" : | Specifies a collection of values used for Amazon S3 encryption with the EMR File System \(EMRFS\)\. | 
| "EncryptionMode" : "SSE\-S3" | "SSE\-KMS" | "CSE\-KMS" | "CSE\-Custom" | Specifies the type of Amazon S3 encryption to use\. If SSE\-S3 is specified, no further Amazon S3 encryption values are required\. If either SSE\-KMS or CSE\-KMS is specified, an AWS KMS customer master key \(CMK\) ARN must be specified as the AwsKmsKey value\. If CSE\-Custom is specified, S3Object and EncryptionKeyProviderClass values must be specified\. | 
| "AwsKmsKey" : "MyKeyARN" | Required only when either SSE\-KMS or CSE\-KMS is specified for EncryptionMode\. MyKeyARN must be a fully specified ARN to a key \(for example, arn:aws:kms:us\-east\-1:123456789012:key/12345678\-1234\-1234\-1234\-123456789012\)\. | 
|  "S3Object" : "JarLocation" | Required only when CSE\-Custom is specified for CertificateProviderType\. JarLocation provides the location in Amazon S3 to a JAR file\. The format can be a path \(for example, s3://MyConfig/articfacts/MyKeyProvider\.jar\) or an ARN \(for example, arn:aws:s3:::Code/MyKeyProvider\.jar\)\. | 
| "EncryptionKeyProviderClass" : "MyS3KeyClassID" | Required only when CSE\-Custom is specified for EncryptionMode\. MyS3KeyClassID specifies a full class name of a class declared in the application that implements the EncryptionMaterialsProvider interface; for example, com\.mycompany\.MyS3KeyProvider\. | 
| Local disk encryption parameters | 
| "LocalDiskEncryptionKeyProvider" | Specifies the key provider and corresponding values to be used for local disk encryption\. | 
| "Type" : "AwsKms" | "Custom" | Specifies the key provider\. If AwsKms is specified, an AWS KMS CMK ARN must be specified as the AwsKmsKey value\. If Custom is specified, S3Object and EncryptionKeyProviderClass values must be specified\. | 
| "AwsKmsKey : "MyKeyARN" | Required only when AwsKms is specified for Type\. MyKeyARN must be a fully specified ARN to a key \(for example, arn:aws:kms:us\-east\-1:123456789012:key/12345678\-1234\-1234\-1234\-456789012123\)\. | 
| "S3Object" : "JarLocation" | Required only when CSE\-Custom is specified for CertificateProviderType\. JarLocation provides the location in Amazon S3 to a JAR file\. The format can be a path \(for example, s3://MyConfig/articfacts/MyKeyProvider\.jar\) or an ARN \(for example, arn:aws:s3:::Code/MyKeyProvider\.jar\)\. | 
|  `"EncryptionKeyProviderClass" : "MyLocalDiskKeyClassID"`  | Required only when Custom is specified for Type\. MyLocalDiskKeyClassID specifies a full class name of a class declared in the application that implements the EncryptionMaterialsProvider interface; for example, com\.mycompany\.MyLocalDiskKeyProvider\. | 

## Configure Kerberos Authentication<a name="emr-security-configuration-kerberos"></a>

A security configuration with Kerberos settings can only be used by a cluster that is created with Kerberos attributes or an error occurs\. For more information, see [Configure Kerberos](emr-kerberos-configure.md)\. Kerberos is only available in Amazon EMR release version 5\.10\.0 and later\.

### Specifying Kerberos Settings Using the Console<a name="emr-security-configuration-console-kerberos"></a>

Choose options under **Kerberos authentication** according to the following guidelines\.


| Parameter | Description | 
| --- | --- | 
|  **Enable Kerberos**  | Specifies that Kerberos is enabled for clusters that use this security configuration\. If a cluster uses this security configuration, the cluster must also have Kerberos settings specified or an error occurs\. | 
|   **Ticket Lifetime**  |  The period for which a Kerberos ticket issued by the cluster\-dedicated KDC is valid\. Ticket lifetimes are limited for security reasons\. Cluster applications and services auto\-renew tickets after they expire\. Users who connect to the cluster over SSH using Kerberos credentials need to run `kinit` from the master node command line to renew after a ticket expires\.  | 
|  **Cross\-realm trust**  |  If you provide a cross\-realm trust configuration, principals \(typically users\) from another realm are authenticated to clusters that use this configuration\. Additional configuration in the other Kerberos realm is also required\. For more information, see [Configure a Cross\-Realm Trust](emr-kerberos-cross-realm.md)\.  | 
|   **Admin server**  |  The fully qualified domain name \(FQDN\) of the other Kerberos admin server in the trust relationship\. The admin server and KDC typically run on the same server\. Optionally, you can specify the port used to communicate with Kerberos admin server\. If not specified, port 749 is used, which is the Kerberos default\.   | 
|   **KDC server**  |  The fully qualified domain name \(FQDN\) of the KDC in the other realm of the trust relationship\. Optionally, you can specify the port used to communicate with the KDC server\. If not specified, port 88 is used, which is the Kerberos default\.  | 
|   **Domain name**  |  The domain name of the other realm in the trust relationship\.  | 

### JSON Reference for Kerberos Settings<a name="emr-kerberos-cli-parameters"></a>

The following example shows JSON parameters for a Kerberos configuration, followed by a reference of parameters and values\.

```
{
    "AuthenticationConfiguration": {
        "KerberosConfiguration": {
            "Provider": "ClusterDedicatedKdc",
            "ClusterDedicatedKdcConfiguration": {
                "TicketLifeTimeInHours": number,
                "CrossRealmTrustConfiguration": {
                    "Realm": "DOMAIN.EXAMPLE.COM",
                    "Domain": "domain.example.com",
                    "AdminServer": "domain.example.com",
					"KdcServer": "domain.example.com"
                }
            }
        }
    }
}
```


| Parameter | Description | 
| --- | --- | 
| "AuthenticationConfiguration" : | Required\. Contains Kerberos configuration parameters\. | 
| "KerberosConfiguration" : | Required\. Contains Kerberos configuration parameters\. | 
| "Provider": "ClusterDedicatedKdc" | Required\. Specifies that a cluster\-dedicated KDC is created on the master node\. | 
| "ClusterDedicatedKdcConfiguration" | Required\. Contains configuration parameters for the cluster\-dedicated KDC on the master node\. | 
| "TicketLifeTimeInHours": number | Optional\. Specifies the period for which a Kerberos ticket issued by the cluster\-dedicated KDC is valid\. Ticket lifetimes are limited for security reasons\. Cluster applications and services auto\-renew tickets after they expire\. Users who connect to the cluster over SSH using Kerberos credentials need to run kinit from the master node command line to renew after a ticket expires\. If omitted, defaults to 24\. | 
| "CrossRealmTrustConfiguration": | Optional\. Contains parameters that define a cross\-realm trust configuration\. | 
| "Realm": "DOMAIN\.EXAMPLE\.COM" | Specifies the Kerberos realm name of the other realm in the trust relationship\. Any string can be used, but this is typically the same as the Domain, but in all capital letters\. | 
| "Domain": "domain\.example\.com"  |  Specifies the domain name of the other realm in the trust relationship\.  | 
| "AdminServer": "domain\.example\.com" | Specifies the fully qualified domain name \(FQDN\) of the admin server in the other realm of the trust relationship\. The admin server and KDC server typically run on the same machine with the same FQDN, but communicate on different ports\. If no port is specified, port 749 is used, which is the Kerberos default\. Optionally, you can specify the port \(for example, domain\.example\.com:749\)\.  | 
| "KdcServer": "domain\.example\.com" | Specifies the fully qualified domain name \(FQDN\) of the KDC server in the other realm of the trust relationship\. The KDC server and admin server typically run on the same machine with the same FQDN, but communicate on different ports\. If no port is specified, port 88 is used, which is the Kerberos default\. Optionally, you can specify the port \(for example, domain\.example\.com:88\)\.  | 

## Configure S3 Authorization for EMRFS<a name="emr-security-configuration-emrfs"></a>

EMRFS S3 authorization provides a way for you to provide dynamic credentials for users to access EMRFS data in Amazon S3\. You create role mappings specifying an IAM role that is assumed when the access request contains an identifier that you specify\. The identifier can be a Hadoop user or role, or an Amazon S3 prefix\. For more information, see [Configure EMRFS Authorization for Data in Amazon S3](emr-emrfs-authz.md)\.

The following is an example JSON snippet for EMRFS authorization within a security configuration\. It demonstrates role mappings for the three different identifier types, followed by a parameter reference\. 

```
{
  "AuthorizationConfiguration": {
    "EmrFsConfiguration": {
      "RoleMappings": [{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_for_user1",
        "IdentifierType": "User",
        "Identifiers": [ "user1" ]
      },{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_to_MyBuckets",
        "IdentifierType": "Prefix",
        "Identifiers": [ "s3://MyBucket/","s3://MyOtherBucket/" ]
      },{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_for_AdminGroup",
        "IdentifierType": "Group",
        "Identifiers": [ "AdminGroup" ]
      }]
    }
  }
}
```


| Parameter | Description | 
| --- | --- | 
|  `"AuthorizationConfiguration":`  |  Required for EMRFS authorization\. Contains authorization configurations\.  | 
|   `"EmrFsConfiguration":`  |  Required for EMRFS authorization\. Contains EMRFS authorization configurations\.  | 
|    `"RoleMappings":`  |  Required for EMRFS authorization\. Contains one or more role mapping definitions\. Role mappings are evaluated in the top\-down order that they appear\. If a role mapping evaluates as true for an EMRFS call for data in Amazon S3, no further role mappings are evaluated\. Role mappings consist of the following required parameters: | 
|    `"Role":` | Specifies the ARN identifier of an IAM role in the format `arn:aws:iam::account-id:role/role-name`\. This is the IAM role that Amazon EMR assumes if the EMRFS request to Amazon S3 matches any of the `Identifiers` specified\. | 
|    `"IdentifierType":` | Can be one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-create-security-configuration.html)  | 
|     `"Identifiers":`  |  Specifies one or more identifiers of the appropriate identifier type\. Separate multiple identifiers by commas with no spaces\.  | 