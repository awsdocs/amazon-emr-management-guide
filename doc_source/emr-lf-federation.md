# Configure a Trust Relationship Between your IdP and Lake Formation<a name="emr-lf-federation"></a>

To establish a trust relationship between your organization's Identity Provider \(IdP\) and AWS, you must do the following:
+ Tell your IdP about AWS as a service provider\. 
+ Tell AWS about your external IdP by creating an IAM identity provider and role for SAML access in AWS Identity and Access Management\. 

This process is called adding a *relying party trust*\. For more information about relying party trusts, see [Configuring your SAML 2\.0 IdP with relying party trust and adding claims](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_relying-party.html) in the AWS Identity and Access Management User Guide\.

**To add a relying party trust between Lake Formation and your IdP**

1. Register AWS with your IdP\. The process of registering AWS with your IdP depends on which IdP you use\. For more information on how to do this for Auth0, Microsoft Active Directory Federation Services \(AD FS\), and Okta, see [Configure Third\-Party Providers for SAML](emr-lf-idp.md)\. 

1. Using your IdP, generate a metadata XML file that can describe your IdP as an IAM identity provider in AWS\. Your IdP metadata XML file must include the following items:
   + Issuer name
   + Creation date
   + Expiration date
   + Keys that AWS will use to validate authentication responses \(assertions\) from your organization\.

   Each IdP has a specific way of simply exporting this metadata\. For more information, refer to the documentation for your IdP\.

   Upload the metadata XML file to an Amazon S3 bucket\. When you launch a cluster that integrates with Lake Formation, you will specify the path to the S3 bucket\. 

1. Create a SAML Identity Provider\. 

   1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. In the navigation pane, choose **Identity Providers**, **Create Provider**\.

   1. For **Provider Type**, choose **Choose a provider type**, **SAML**\.

   1. Enter a name for the identity provider\. 

   1. For **Metadata Document**, click **Choose File**, specify the SAML metadata document that you downloaded from your IdP in the previous step, and choose **Open**\. 

   1. Verify the information that you have provided, and click **Create**\.

1. Create an IAM role for Lake Formation\. 

   1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. In the navigation pane, choose **Roles**, **Create role**\.

   1. Choose the **SAML 2\.0 federation** role type\.

   1. For **SAML Provider**, choose the provider for your role\.

   1. Choose **Allow programmatic and AWS Management Console access** to create a role that can be assumed programmatically and from the console\.

   1. Review your SAML 2\.0 trust information, then choose **Next: Permissions**\. 

   1. Create the permissions policy for the role based on the example below\. For more information about this permissions policy, see [IAM Roles for Lake Formation](emr-lf-iam-role.md)\. 

      ```
      {
        "Version": "2012-10-17",
        "Statement": {
          "Effect": "Allow",
          "Action": [
            "lakeformation:GetDataAccess",
            "lakeformation:GetMetadataAccess",
            "glue:GetUnfiltered*",
            "glue:GetTable",
            "glue:GetTables",
            "glue:GetDatabase",
            "glue:GetDatabases",
            "glue:CreateDatabase",
            "glue:GetUserDefinedFunction",
            "glue:GetUserDefinedFunctions"
          ],
          "Resource": "*"
        }
      }
      ```
**Note**  
Do not grant this role permissions to access any Amazon S3 buckets managed by AWS Glue\. The federated user should access data through Lake Formation using Spark SQL and should not access data directly through Amazon S3\.

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review**\.

   1. For **Role name**, type a role name\. Role names must be unique within your AWS account\. 

   1. Review the role and then choose **Create role**\.

   1. Click Roles tab, search for the role name created from the last step\.

   1. Choose **Trust relationships**, and then select **Edit trust relationship**\.

   1. Override the existing policy document with the IAM Role for Lake Formation trust policy specified below\. For more information about this trust policy, see [IAM Roles for Lake Formation](emr-lf-iam-role.md) section\. 

      Replace *account\-id* with your AWS account ID\. Replace the *IAM\_identity\_provider\_name* with your IAM identity provider’s name\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Principal": {
                      "Federated": "arn:aws:iam::account-id:saml-provider/IAM_identity_provider_name"
                  },
                  "Action": "sts:AssumeRoleWithSAML"
              }
          ]
      }
      ```

   1. Choose **Update Trust Relationship**\. 

1. Create an IAM role for other AWS services\. For more information, see [IAM Role for Non\-Lake Formation AWS Services](emr-lf-iam-role.md#emr-lf-iam-role-AWS-services)\.

1. In your organization's IdP, you must configure SAML assertions that map the users in your organization to the IdP and the IAM role for Lake Formation that you just created\. Do this by configuring the three attribute elements shown in the following table\. 
   + Replace *account\-id* with your AWS account ID\.
   + Replace *IAM\_Role\_For\_Lake\_Formation* with the name of the IAM role for Lake Formation that you created\.
   + Replace *IAM\_identity\_provider\_name* with the name of the IAM identity provider that you created in the previous steps\. 
   + Replace *user\_alias* with the name of the attribute used to hold the user name defined in your organization\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-lf-federation.html)

   The exact steps to perform this mapping depend on which IdP you use\. For instructions on how to map your IdP, see [Configure Third\-Party Providers for SAML](emr-lf-idp.md)\.

   For more information, see [Configuring SAML Assertions for the Authentication Response](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_assertions.html)\.

**Note**  
A user can get Lake Formation privileges for all the groups they belong to\. The list of groups a user belongs to can be sent in a custom SAML attribute that is specific to Lake Formation, as the following example demonstrates\.   

```
<AttributeStatement> 
<Attribute Name="https://lakeformation.amazon.com/SAML/Attributes/Groups"> 
<AttributeValue>group1</AttributeValue> 
<AttributeValue>group2</AttributeValue> 
</Attribute> 
</AttributeStatement>
```