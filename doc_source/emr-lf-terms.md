# Terms and concepts<a name="emr-lf-terms"></a>

This section provides definitions for concepts and terms that are used in the context of integrating Amazon EMR with AWS Lake Formation\. 

**Authentication**

The process of establishing the identity of a user\. By integrating Amazon EMR and Lake Formation, your users can use their corporate credentials to log into EMR Notebooks and Apache Zeppelin\. 

**Authorization**

The process of permitting which actions a given user can take on a resource\. When integrating an Amazon EMR cluster with Lake Formation, you define policies that authorize user access to databases, tables, and columns\. This process ensures users may only query and analyze the tables or columns for which they're authorized\. 

**Federation**

The creation of a trust relationship between an external identity provider \(IdP\) and AWS Identity and Access Management\(IAM\)\. Users can sign in through a compatible, Security Assertion Markup Language \(SAML\) 2\.0 identity provider \(IdP\), such as Microsoft Active Directory Federation Services\. For more information, see [Configure third\-party providers for SAML](emr-lf-idp.md)\. When you use SAML 2\.0 to configure a trust relationship between an IdP and IAM, AWS assigns the user an IAM role\. The user also receives temporary credentials that allow the user to access your AWS Lake Formation resources\.

**Trust policy**

A document in [JSON](http://www.json.org/) format that defines who is allowed to assume an IAM role\. The document is written according to the rules of the [IAM policy language](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\.

**Permissions policy**

A permissions document in [JSON](http://www.json.org/) format that defines the actions and resources to which an IAM role has access\. The document is written according to the rules of the [IAM policy language](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\.

**Principal**

An entity that can access resources protected by Lake Formation policies and run queries in Amazon EMR\. Principals are federated users\.