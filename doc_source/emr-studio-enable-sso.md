# Enable AWS Single Sign\-On for Amazon EMR Studio<a name="emr-studio-enable-sso"></a>

## About AWS SSO for EMR Studio<a name="emr-studio-about-sso"></a>

EMR Studio uses AWS Single Sign\-On \(SSO\) to provide access to a Studio with a unique sign\-in URL\. You must enable AWS SSO authentication, connect AWS SSO to your identity provider \(IdP\), and import your users and groups using the AWS SSO console\. After you enable SSO, you assign users and groups to an EMR Studio using the AWS CLI\.

EMR Studio currently supports using the following identity providers:
+ **SAML\-based providers** – For a full list, see [Supported Identity Providers](https://docs.aws.amazon.com/singlesignon/latest/userguide/supported-idps.html)\.
**Note**  
EMR Studio does not currently support using self\-managed Microsoft Active Directory or AWS Managed Microsoft AD with AWS Single Sign\-On\. Azure AD is supported\.
+ **The AWS Single Sign\-On store** – For more information, see [Manage Identities in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-sso.html)\.

## Prerequisites<a name="emr-studio-sso-prereqs"></a>

To enable AWS SSO for EMR Studio, you need the following:
+ A management account in your AWS Organization if you use multiple accounts in your organization\. 
**Note**  
Enabling SSO and importing users and groups are the only steps you should take using your management account\. After you set up SSO, you use a member account to create an EMR Studio and assign users and groups\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ If you enabled AWS SSO prior to November 25, 2019, you might need to enable AWS SSO\-integrated applications for the accounts in your AWS organization\. For more information, see [Enable AWS SSO\-Integrated Applications in AWS Accounts](https://docs.aws.amazon.com/singlesignon/latest/userguide/app-enablement.html#enable-app-enablement)\.

## Instructions<a name="emr-studio-sso-instructions"></a>

Follow the directions in the [AWS SSO Getting Started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) tutorial to enable AWS SSO and connect to your third\-party identity provider \(IdP\)\.

Import users and groups following the instructions provided in the [Manage Identities in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-sso.html) section of the *AWS Single Sign\-On User Guide*\.