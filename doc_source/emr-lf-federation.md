# Configure Trust Relationship Between your IdP and Lake Formation<a name="emr-lf-federation"></a>

To establish a trust relationship between your organization's Identity Provider \(IdP\) and AWS, you must do the following:
+ Tell your IdP about AWS as a service provider by adding relying party trust between IdP and AWS\. 
+ Tell AWS about your external IdP by creating an IAM identity provider and role for SAML access in AWS IAM\. 

**To configure this trust relationship**

1. Register AWS with your IdP\. The process of registering AWS with your IdP depends on which IdP you're using\. For more information on how to do this for Auth0, Microsoft Active Directory Federation Services \(AD FS\), and Okta, see [Supported Third\-Party Providers for SAML](emr-lf-idp.md)\. 

1. Using your IdP, generate a metadata XML file that can describe your IdP as an IAM identity provider in AWS\. It must include the issuer name, a creation date, an expiration date, and keys that AWS uses to validate authentication responses \(assertions\) from your organization\. Each IdP has a specific way of simply exporting this metadata\. For more information, refer to your IdP’s documentation\.

   You must upload the metadata XML file to an Amazon S3 bucket\. When you launch a cluster that integrates with Lake Formation, you need to specify the path to the S3 bucket\. 

1. In the IAM console, create a SAML identity provider entity\. 

   1. Sign in to the AWS Management Console and open the IAM console at https://console\.aws\.amazon\.com/iam/\.

   1. In the navigation pane, choose **Identity Providers**, **Create Provider**\.

   1. For **Provider Type**, choose **Choose a provider type**, **SAML**\.

   1. Enter a name for the identity provider\. 

   1. For **Metadata Document**, click **Choose File**, specify the SAML metadata document that you downloaded from your IdP in the previous step, and choose **Open**\. 

   1. Verify the information that you have provided, and click **Create**\.

1. In the IAM console, create an IAM role for identity federation\. 

   1. In the navigation pane of the IAM console, choose **Roles**, **Create role**\.

   1. Choose the **SAML 2\.0 federation** role type\.

   1. For **SAML Provider**, choose the provider for your role\.

   1. Choose **Allow programmatic and AWS Management Console access** to create a role that can be assumed programmatically and from the console\.

   1. Review your SAML 2\.0 trust information, then choose **Next: Permissions**\. 

   1. Create the permissions policy for the role based on the example in [Overview of the IAM Roles for Lake Formation](emr-lf-iam-role.md)\. 

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review**\.

   1. For **Role name**, type a role name\. Role names must be unique within your AWS account\. 

   1. Review the role and then choose **Create role**\.

   1. Click Roles tab, search for the role name created from the last step\.

   1. Choose **Trust relationships**, and then select **Edit trust relationship**\.

   1. Override the existing policy document with the IAM Role for Lake Formation trust policy specified in the [Overview of the IAM Roles for Lake Formation](emr-lf-iam-role.md) section\. Then click **Update Trust Relationship**\. 

1. In your organization's IdP, you must configure SAML assertions that map the users in your organization to the Identity Provider and the IAM role for Lake Formation that was just created\. You do this by configuring the three attribute elements shown in the following table\. 
   + Replace *account\-id* with your AWS account ID\.
   + Replace *IAM\_Role\_For\_Lake\_Formation* with the name of the IAM role for Lake Formation that you created\.
   + Replace *IAM\_identity\_provider\_name* with the name of the IAM identity provider that you created in previous steps\. 
   + Replace *user\_alias* with the name of the attribute used to hold the user name defined in your organization\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-lf-federation.html)

   The exact steps for performing the mapping depend on which IdP you're using\. For more information, see the next section [Supported Third\-Party Providers for SAML](emr-lf-idp.md)\.

   For more information, see [Configuring SAML Assertions for the Authentication Response](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_assertions.html)\.