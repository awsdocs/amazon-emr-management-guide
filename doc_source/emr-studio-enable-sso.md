# Enable AWS Single Sign\-On for Amazon EMR Studio<a name="emr-studio-enable-sso"></a>

## About AWS SSO for EMR Studio<a name="emr-studio-about-sso"></a>

EMR Studio uses AWS Single Sign\-On \(AWS SSO\) to provide access to a Studio with a unique sign\-in URL\. You must enable AWS SSO, configure your identity source, and provision users and groups\. Provisioning is the process of making user and group information available for use by AWS SSO and by applications that use AWS SSO\. For more information, see [User and group provisioning](https://docs.aws.amazon.com/singlesignon/latest/userguide/users-groups-provisioning.html#user-group-provision)\. 

You do not use the AWS SSO console to assign users or groups to your EMR Studio\. After you complete the instructions on this page, you can create a Studio and assign users and groups from your AWS SSO store to the Studio using the Amazon EMR console or the AWS CLI\. 

**Note**  
EMR Studio uses IAM session policies to manage Studio permissions at the user and group level\. EMR Studio maps a session policy to a user or group when you assign the user or group to your Studio\. For more information, see [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)\.

EMR Studio currently supports using the following identity providers:
+ **AWS Managed Microsoft AD and self\-managed Active Directory** – For more information, see [Connect to Your Microsoft AD Directory](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-ad.html)\.
+ **SAML\-based providers** – For a full list, see [Supported Identity Providers](https://docs.aws.amazon.com/singlesignon/latest/userguide/supported-idps.html)\.
+ **The AWS Single Sign\-On store** – For more information, see [Manage Identities in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-sso.html)\.

## Prerequisites<a name="emr-studio-sso-prereqs"></a>

Before you set up AWS SSO for EMR Studio, you need the following:
+ A management account in your AWS organization if you use multiple accounts in your organization\. 
**Note**  
Enabling AWS SSO and provisioning users and groups are the only steps you should take using your management account\. After you set up AWS SSO, you use a member account to create an EMR Studio and assign users and groups\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ If you enabled AWS SSO prior to November 25, 2019, you might need to enable applications that use AWS SSO for the accounts in your AWS organization\. For more information, see [Enable AWS SSO\-Integrated Applications in AWS Accounts](https://docs.aws.amazon.com/singlesignon/latest/userguide/app-enablement.html#enable-app-enablement)\.
+ Make sure you have the prerequisites listed on the [AWS SSO prerequisites](https://docs.aws.amazon.com/singlesignon/latest/userguide/prereqs.html) page\.

## Instructions<a name="emr-studio-sso-instructions"></a>

**To set up AWS SSO for EMR Studio**

1. Follow the instructions in [Enable AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/step1.html) to enable AWS SSO in the AWS Region where you want to create your EMR Studio\.

1. Connect AWS SSO to your identity provider and provision the users and groups that you want to assign to your Studio\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-enable-sso.html)

You can now assign users and groups from your AWS identity store to your EMR Studio\. For more information about how to assign users and groups to a Studio, see [Assign a User or Group to Your EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.