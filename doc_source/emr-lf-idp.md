# Supported Third\-Party Providers for SAML<a name="emr-lf-idp"></a>

Integration between Amazon EMR and AWS Lake Formation supports SAML 2\.0\-based federation with the following third\-party providers: Microsoft Active Directory Federation Services \(AD FS\), Auth0, and Okta\. The following sections provide information to help you configure these IdPs to work with AWS Lake Formation federation\.

## Auth0<a name="emr-lf-auth0"></a>

[AWS Integration in Auth0](https://auth0.com/docs/integrations/aws) – This page on the Auth0 documentation website describes how to set up single sign\-on \(SSO\) with the AWS Management Console\. It also includes a JavaScript example\.

To enable federated access to Lake Formation, customize the following steps in the Auth0 documentation:
+ When providing an application callback URL, provide a temporary URL, as shown in the following example\. Update *public\-dns* with the actual DNS name for your master node after launching your cluster\. 

  ```
  https://public-dns:8442/gateway/knoxsso/api/v1/websso?pac4jCallback=true&client_name=SAML2Client 
  ```
+ When configuring SAML, paste the following SAML configuration code into **Settings**\.

  ```
  {
      "audience": "urn:amazon:webservices",
      "mappings": {
          "email": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress",
          "name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
      },
      "createUpnClaim": false,
      "passthroughClaimsWithNoMapping": false,
      "mapUnknownClaimsAsIs": false,
      "mapIdentities": false,
      "nameIdentifierFormat": "urn:oasis:names:tc:SAML:2.0:nameid-format:persistent",
      "nameIdentifierProbes": [
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
      ]
  }
  ```
+ When mapping the AWS role to a user, create a rule with the following code\. Replace the *IAM\_Role\_For\_Lake\_Formation* with the name of the IAM role for Lake Formation that you created\. Replace the *IAM\_identity\_provider\_name* with the name of the IAM identity provider that you created for Auth0\. 

  ```
  function (user, context, callback) {
    user.awsRole = 'arn:aws:iam::account-id:role/IAM_Role_For_Lake_Formation,arn:aws:iam::account-id:saml-provider/IAM_identity_provider_name';
    // the username must not contain "@" - as it is not a valid Linux username
    user.glueUser = user.name.replace(/@.*/, ''); 
   
    context.samlConfiguration.mappings = {
      'https://aws.amazon.com/SAML/Attributes/Role': 'awsRole',
      'https://aws.amazon.com/SAML/Attributes/RoleSessionName': 'glueUser',
      'https://lakeformation.amazon.com/SAML/Attributes/Username': 'glueUser'
    };
   
    callback(null, user, context);
  }
  ```

## Microsoft Active Directory Federation Services \(AD FS\)<a name="emr-lf-adfs"></a>

[AWS Federated Authentication with Active Directory Federation Services \(AD FS\)](https://aws.amazon.com/blogs/security/aws-federated-authentication-with-active-directory-federation-services-ad-fs/) – This post on the AWS Security Blog shows how to configure AD FS and enable SAML federation with AWS\.

To enable federated access to Lake Formation, customize the following steps:
+ To add relying party trust, manually enter data about the relying party instead of importing metadata from the existing URL\. Select the **Permit all users to access this relying party** option\. For the endpoint trusted URL, provide a temporary URL, as shown in the following example\. Update *public\-dns* with the actual DNS name for your master node after launching your cluster\. 

  ```
  https://public-dns:8442/gateway/knoxsso/api/v1/websso?pac4jCallback=true&client_name=SAML2Client 
  ```
+ In the step of **Edit Claim Issuance Policy**, customize the three rules `NameId`, `RoleSessionName`, and `Role` based on the values for the attribute elements in [Configure Trust Relationship Between your IdP and Lake Formation](emr-lf-federation.md)\.

## Okta<a name="emr-lf-okta"></a>

[Set up a SAML Application in Okta](https://developer.okta.com/docs/guides/saml-application-setup/overview/) – From this page on the Okta support site you can learn how to configure Okta by providing metadata about the relying party\.

To enable federated access to Lake Formation, customize the following steps:
+ When configuring SAML, for the **Single sign\-on URL**, use the temporary URL, as shown in the following example\. Update the *public\-dns* with the actual DNS name for your master node after launching your cluster\.

  ```
  https://public-dns:8442/gateway/knoxsso/api/v1/websso?pac4jCallback=true&client_name=SAML2Client
  ```
+ For the **Audience URI \(SP Entity ID\)** box, fill in `urn:amazon:webservices`\.
+ In the **Attribute Statements** section, add three attribute statements as demonstrated in the following procedure\. Replace the *IAM\_Role\_For\_Lake\_Formation* with the name of the IAM role for Lake Formation that you created\. Replace the *IAM\_identity\_provider\_name* with the name of the IAM identity provider that you created in previous steps\. Replace *user\_alias* with the name of the attribute used to hold the user name defined in your organization\.

  1. Name: `https://aws.amazon.com/SAML/Attributes/Role`

     Value: `arn:aws:iam::account-id:role/IAM_Role_For_Lake_Formation,arn:aws:iam::account-id:saml-provider/IAM_identity_provider_name` 

  1. Name: `https://aws.amazon.com/SAML/Attributes/RoleSessionName`

     Value: `user_alias`

  1. Name: `https://glue.amazon.com/SAML/Attributes/UserName`

     Value: `user_alias`