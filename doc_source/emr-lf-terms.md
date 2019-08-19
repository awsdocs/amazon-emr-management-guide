# Terms and Concepts<a name="emr-lf-terms"></a>

This section gives an overview of the concepts and terms that are used in the context of integrating Amazon EMR with AWS Lake Formation\. 

**Authentication**

The process of establishing the identity of a user\. By integrating Amazon EMR and Lake Formation, your users can use their corporate credentials to log into EMR Notebooks and Apache Zeppelin\. 

**Authorization**

The process of validating the actions a given user can take on certain resources\. When integrating an Amazon EMR cluster with Lake Formation, access to databases and tables is authorized by using Lake Formation policies\. This process ensures users can only query and analyze the tables or columns that they are authorized to access\. 

**Federation**

The creation of a trust relationship between an external identity provider and AWS Identity and Access Management\(IAM\)\. Users can sign in to an enterprise identity system that is compatible with Security Assertion Markup Language \(SAML\) 2\.0, such as Microsoft Active Directory Federation Services\. For more information, see [Supported Third\-Party Providers for SAML](emr-lf-idp.md)\. When you use SAML 2\.0 to configure a trust relationship between these external identity providers and IAM, the user is assigned to an IAM role\. The user also receives temporary credentials that allow the user to access your AWS Lake Formation resources\.

**Trust policy**

A document in [JSON](http://www.json.org/) format in which you define who is allowed to assume the role\. This trusted entity is included in the policy as the principal element in the document\. The document is written according to the rules of the [IAM policy language](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\.

**Permissions policy**

A permissions document in [JSON](http://www.json.org/) format in which you define the actions and resources the role has access to\. The document is written according to the rules of the [IAM policy language](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html)\.

**Principal**

An entity that can access resources protected by Lake Formation policies and run queries in Amazon EMR\. Principals can be AWS Identity and Access Management \(IAM\) users or roles, or federated users identified by their SAML Identity Provider \(IdP\)\.