# Choose an authentication mode for Amazon EMR Studio<a name="emr-studio-authentication"></a>

This section describes the authentication \(sign in\) options for Amazon EMR Studio with guidance to help you choose and set up Studio authentication\. 

Amazon EMR Studio supports two authentication modes: IAM authentication mode and SSO authentication mode\. IAM mode uses AWS Identity and Access Management \(IAM\), while SSO mode uses AWS Single Sign\-On \(AWS SSO\)\. When you create an EMR Studio, you choose the authentication mode for all users of that Studio\.

The following table outlines some considerations for choosing an authentication mode for EMR Studio\.


****  

| If you are\.\.\. | We recommend\.\.\. | 
| --- | --- | 
| Already familiar with or have previously set up IAM authentication or federation |  [IAM authentication mode](#emr-studio-iam-authentication), which offers the following benefits: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html)  | 
| New to AWS or Amazon EMR | [SSO authentication mode](#emr-studio-enable-sso), which provides the following features:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html) | 

## IAM authentication mode for Amazon EMR Studio<a name="emr-studio-iam-authentication"></a>

With IAM authentication mode, you can use either IAM authentication or IAM federation\. IAM *authentication* lets you manage IAM identities such as users, groups, and roles in IAM\. You grant users access to a Studio with IAM permissions policies and [attribute\-based access control \(ABAC\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html)\. IAM *federation* lets you establish trust between a third\-party identity provider \(IdP\) and AWS so that you can manage user identities through your IdP\.

**Note**  
If you already use IAM to control access to AWS resources, or if you've already configured your identity provider \(IdP\) for IAM, see [User permissions for IAM authentication mode](emr-studio-user-permissions.md#emr-studio-iam-authorization) to set user permissions when you use IAM authentication mode for EMR Studio\.

### Use IAM federation for Amazon EMR Studio<a name="emr-studio-iam-federation"></a>

To use IAM federation for Amazon EMR Studio, you create a trust relationship between your AWS account and your identity provider \(IdP\) and enable federated users to access the AWS Management Console\. The steps you take to create this trust relationship differ depending on your IdP's federation standard\.

In general, you complete the following tasks to configure federation with an external IdP\. For complete instructions, see [Enabling SAML 2\.0 federated users to access the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_enable-console-saml.html) and [Enabling custom identity broker access to the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_enable-console-custom-url.html) in the *AWS Identity and Access Management User Guide*\.

1. Gather information from your IdP\. This usually means generating a metadata document to validate SAML authentication requests from your IdP\.

1. Create an identity provider IAM entity to store information about your IdP\. For instructions, see [Creating IAM identity providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create.html)\.

1. Create one or more IAM roles for your IdP\. EMR Studio assigns a role to a federated user when the user logs in\. The role permits your IdP to request temporary security credentials for access to AWS\. For instructions, see [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html)\. The permissions policies that you assign to the role determine what federated users can do in AWS and in an EMR Studio\. For more information, see [User permissions for IAM authentication mode](emr-studio-user-permissions.md#emr-studio-iam-authorization)\.

1. \(For SAML providers\) Complete the SAML trust by configuring your IdP with information about AWS and the roles that you want federated users to assume\. This configuration process creates *relying party trust* between your IdP and AWS\. For more information, see [Configuring your SAML 2\.0 IdP with relying party trust and adding claims](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml_relying-party.html)\.

**To configure an EMR Studio as a SAML application in your IdP portal**

You can configure a particular EMR Studio as a SAML application using a deep link to the Studio\. Doing so lets users log in to your IdP portal and launch a specific Studio instead of navigating through the Amazon EMR console\.
+ Use the following format to configure a deep link to your EMR Studio as a landing URL after SAML assertion verification\. 

  ```
  https://console.aws.amazon.com/emr/home?region=<aws-region>#studio/<your-studio-id>/start
  ```

## SSO authentication mode for Amazon EMR Studio<a name="emr-studio-enable-sso"></a>

SSO authentication mode lets you give users federated access to an EMR Studio\. You can use AWS SSO to authenticate users and groups from your AWS SSO directory, your existing corporate directory, or an external IdP such as Azure Active Directory \(AD\)\. You then manage users with your identity provider \(IdP\)\.

EMR Studio supports using the following identity providers for AWS SSO:
+ **AWS Managed Microsoft AD and self\-managed Active Directory** – For more information, see [Connect to your Microsoft AD directory](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-ad.html)\.
+ **SAML\-based providers** – For a full list, see [Supported identity providers](https://docs.aws.amazon.com/singlesignon/latest/userguide/supported-idps.html)\.
+ **The AWS Single Sign\-On store** – For more information, see [Manage identities in AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-sso.html)\.

To prepare AWS Single Sign\-On \(AWS SSO\) for EMR Studio, you must configure your identity source and provision users and groups\. Provisioning is the process of making user and group information available for use by AWS SSO and by applications that use AWS SSO\. For more information, see [User and group provisioning](https://docs.aws.amazon.com/singlesignon/latest/userguide/users-groups-provisioning.html#user-group-provision)\. 

After you create a Studio, you can assign users and groups to the Studio using the Amazon EMR console or the AWS CLI\.

**Note**  
With SSO authentication mode, EMR Studio uses IAM session policies to manage Studio permissions at the user and group level\. You map a session policy to a user or group when you assign the user or group to a Studio\. For more information, see [User permissions for AWS SSO authentication mode](emr-studio-user-permissions.md#emr-studio-sso-authorization)\.

**To set up AWS SSO for EMR Studio**

1. To set up AWS SSO for EMR Studio, you need the following:
   + A management account in your AWS organization if you use multiple accounts in your organization\. 
**Note**  
You should only use your management account to enable AWS SSO and *provision* users and groups\. After you set up AWS SSO, use a member account to create an EMR Studio and *assign* users and groups\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
   + If you enabled AWS SSO before November 25, 2019, you might have to enable applications that use AWS SSO for the accounts in your AWS organization\. For more information, see [Enable AWS SSO\-integrated applications in AWS accounts](https://docs.aws.amazon.com/singlesignon/latest/userguide/app-enablement.html#enable-app-enablement)\.
   + Make sure that you have the prerequisites listed on the [AWS SSO prerequisites](https://docs.aws.amazon.com/singlesignon/latest/userguide/prereqs.html) page\.

1. Follow the instructions in [Enable AWS SSO](https://docs.aws.amazon.com/singlesignon/latest/userguide/step1.html) to enable AWS SSO in the AWS Region where you want to create the EMR Studio\.

1. Connect AWS SSO to your identity provider and provision the users and groups that you want to assign to the Studio\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html)

You can now assign users and groups from your AWS identity store to an EMR Studio\. For instructions, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

## Login, user assignment, and user permissions<a name="emr-studio-login-auth-differences"></a>

The authentication mode that you choose for Amazon EMR Studio affects how users log in to a Studio, how you assign a user to a Studio, and how you *authorize* \(give permissions to\) users to perform actions such as creating new Amazon EMR clusters\.

The following table summarizes login methods for EMR Studio according to authentication mode\.


**Amazon EMR Studio login options by authentication mode**  

| Authentication mode | Login method | Description | 
| --- | --- | --- | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html) | EMR Studio URL |  Users log in to a Studio using the Studio access URL\. For example, `https://xxxxxxxxxxxxxxxxxxxxxxx.emrstudio-prod.us-east-1.amazonaws.com`\.  Users enter IAM credentials when you use IAM authentication\. When you use IAM federation or AWS SSO, EMR Studio redirects users to your identity provider's sign\-in URL to enter credentials\. In the context of identity federation, this login option is called Service Provider \(SP\) initiated sign\-in\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html)  | Identity provider \(IdP\) portal |  Users log in to your identity provider's portal, such as the Azure portal, and launch the Amazon EMR console\. After launching the Amazon EMR console, users select and open a Studio from the **Studios list**\. You can also configure EMR Studio as a SAML application so that users can log in to a specific Studio from your identity provider's portal\. For instructions, see [To configure an EMR Studio as a SAML application in your IdP portal](#emr-studio-create-federation-deeplink)\. In the context of identity federation, this login option is called identity provider \(IdP\) initiated sign\-in\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-authentication.html)  | AWS Management Console | Users sign in to the AWS Management Console using IAM credentials and open a Studio from the Studios list in the Amazon EMR console\. | 

The following table outlines user assignment and authorization for Amazon EMR Studio by authentication mode\.


**Amazon EMR Studio user assignment and authorization by authentication mode**  

| Authentication mode | User assignment | User authorization | 
| --- | --- | --- | 
|  IAM \(authentication and federation\)  |  Allow the `CreateStudioPresignedUrl` action in an IAM permissions policy attached to an IAM identity \(user, group, or role\)\.  For federated users, allow the `CreateStudioPresignedUrl` action in an IAM in the permissions policy that you configure for the IAM role you use for federation\. Use attribute\-based access control \(ABAC\) to specify the Studio or Studios that the user can access\. For instructions, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.  |  Define IAM permissions policies that allow certain EMR Studio actions\.  For native IAM users, attach the IAM permissions policy to an IAM identity \(user, group, or role\)\. For federated users, allow Studio actions in the permissions policy that you configure for the IAM role you use for federation\. For more information, see [Configure EMR Studio user permissions](emr-studio-user-permissions.md)\.  | 
| AWS SSO |  Assign a user to a Studio by mapping the user to a Studio with a specified session policy\. For instructions, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.  |  Define IAM session policies that allow certain EMR Studio actions\. Map a session policy to a user when you assign the user to a Studio \. For more information, see [User permissions for AWS SSO authentication mode](emr-studio-user-permissions.md#emr-studio-sso-authorization)\.  | 