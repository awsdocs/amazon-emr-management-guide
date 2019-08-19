# Conceptual Overview of Amazon EMR Integration with Lake Formation<a name="emr-lf-conceptual"></a>

By integrating Amazon EMR with AWS Lake Formation, you enable SAML\-based authentication with your corporate credentials and enforce fine\-grained column\-level access control based on policies defined in AWS Lake Formation\.

To integrate Amazon EMR and Lake Formation, your organization must meet the following requirements:
+ Manage your corporate identities using an existing SAML\-based Identity Provider, such as Active Directory Federation Services \(AD FS\)\. For more information, see [Supported Third\-Party Providers for SAML](emr-lf-idp.md)\.
+ Use the AWS Glue Data Catalog as a metadata store\.
+ Use EMR Notebooks or Apache Zeppelin to access data managed by AWS Glue and Lake Formation\. 
+ Define and manage permissions in Lake Formation to access databases, tables, and columns in AWS Glue Data Catalog\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.

Before launching a cluster that integrates with Lake Formation, you need to enable SAML 2\.0\-based federation by configuring your Identity Provider \(IdP\) and AWS Access and Identity Management \(IAM\) roles\. You also need to set up proper security controls for your Amazon EMR cluster\. For more information, see [Before You Begin](emr-lf-prerequisites.md) and [Launch an Amazon EMR Cluster with Lake Formation](emr-lf-launch.md)\.

**Topics**
+ [Terms and Concepts](emr-lf-terms.md)
+ [How Access to Data Works in Lake Formation](emr-lf-data.md)
+ [Amazon EMR Components](emr-lf-components.md)
+ [Architecture of SAML\-Enabled Single Sign\-On and Fine\-Grained Access Control](emr-lf-architecture.md)