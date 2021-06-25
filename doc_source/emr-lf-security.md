# Configure EMR security<a name="emr-lf-security"></a>

## Create an Amazon EMR security configuration for Lake Formation<a name="emr-lf-security-config"></a>

Before you launch an Amazon EMR cluster integrated with AWS Lake Formation, you'll need to create a security configuration with the IAM roles and identity provider \(IdP\) metadata XML file you created in [Configure a trust relationship between your IdP and Lake Formation](emr-lf-federation.md)\. You will specify this security configuration when you launch the cluster\.

------
#### [ Console ]

**To create a security configuration that specifies the **AWS Lake Formation integration** option:**

1. In the Amazon EMR console, select **Security configurations**, then **Create**\.

1. Type a **Name** for the security configuration\. You will use this name to specify the security configuration when you create a cluster\.

1. Under **AWS Lake Formation integration**, select **Enable fine\-grained access control managed by AWS Lake Formation**\.

1. Select your **IAM role for AWS Lake Formation** to apply\.
**Note**  
For more information, see [IAM roles for Lake Formation](emr-lf-iam-role.md)\.

1. Select your **IAM role for other AWS services** to apply\. 

1. Upload your identify provider \(IdP\) metadata by specifying the S3 path where the metadata is located\. 
**Note**  
For more information, see [Configure a trust relationship between your IdP and Lake Formation](emr-lf-federation.md)\.

1. Set up other security configuration options as appropriate and choose **Create**\. You must enable Kerberos authentication using the cluster\-dedicated KDC\. For more information, see [Configure EMR security](#emr-lf-security)\. 

------
#### [ CLI ]

**To create a security configuration for **AWS Lake Formation integration**:**

1. Specify the whole path to your IdP metadata file uploaded in S3\.

1. Replace *account\-id* with your AWS account ID\.

1. Specify a value for `TicketLifetimeInHours` to determine the period for which a Kerberos ticket issued by the KDC is valid\. 

   ```
   {
       "LakeFormationConfiguration": {
           "IdpMetadataS3Path": "s3://mybucket/myfolder/idpmetadata.xml",
           "EmrRoleForUsersARN": "arn:aws:iam::account-id:role/IAM_Role_For_AWS_Services",
           "LakeFormationRoleForSAMLPrincipalARN": "arn:aws:iam::account-id:role/IAM_Role_For_Lake_Formation"
       },
       "AuthenticationConfiguration": {
           "KerberosConfiguration": {
               "Provider": "ClusterDedicatedKdc",
               "ClusterDedicatedKdcConfiguration": {
                   "TicketLifetimeInHours": 24
               }
           }
       }
   }
   ```

1. Create an Amazon EMR security configuration with the following command\. Replace *security\-configuration* with a name of your choice\. You will select this configuration by name when you create your cluster\.

   ```
   aws emr create-security-configuration \
   --security-configuration file://./security-configuration.json \
   --name security-configuration
   ```

------

## Configure additional security features<a name="emr-lf-security-features"></a>

To securely integrate Amazon EMR with AWS Lake Formation, you should also configure the following EMR security features:
+ Enable Kerberos authentication using the cluster\-dedicated KDC\. For instructions, see [Use Kerberos authentication](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-kerberos.html)\.
+ Configure your Amazon EC2 security group or Amazon VPC network access control list \(ACL\) to allow access to the proxy agent \(port 8442\) from your user's desktops\. For more information, see [Control network traffic with security groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html)\.
+ \(Optional\) Enable encryption in transit or at rest\. For more information, see [Encryption options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-data-encryption-options.html) in the *Amazon EMR Management Guide*\. 
+ \(Optional\) Create a custom Transport Layer Security \(TLS\) key pair for the proxy agent\. For more information, see [Customize your proxy agent certificate](emr-lf-notebook.md#emr-lf-TLS)\.

For more information, see [Security in Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security.html)\.