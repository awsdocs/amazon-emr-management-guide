# Launch an Amazon EMR cluster with Lake Formation using the console<a name="emr-lf-console"></a>

1. Create a security configuration that specifies the **AWS Lake Formation integration** option:

   1. In the Amazon EMR console, select **Security configurations**, **Create**\.

   1. Type a **Name** for the security configuration\. You use this name to specify the security configuration when you create a cluster\.

   1. Under **AWS Lake Formation integration**, select **Enable fine\-grained access control managed by AWS Lake Formation**\.

   1. Select an **IAM role for AWS Lake Formation** to apply\.
**Note**  
For more information, see [Overview of the IAM Roles for Lake Formation](emr-lf-iam-role.md)\.

   1. Select an **IAM role for other AWS services** to apply\. 

   1. Upload your identify provider \(IdP\) metadata by specifying the S3 path where the metadata is located\. 
**Note**  
For more information, see [Configure Trust Relationship Between your IdP and Lake Formation](emr-lf-federation.md)\.

   1. Set up other security configuration options as appropriate and choose **Create**\. You must enable Kerberos authentication using the cluster\-dedicated KDC\. For more information, see [Configure EMR Security Features](emr-lf-security.md)\. 

1. Launch a cluster with the security configuration that you specified in the previous step\. For more information, see [Specify a Security Configuration for a Cluster](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-specify-security-configuration.html)\.