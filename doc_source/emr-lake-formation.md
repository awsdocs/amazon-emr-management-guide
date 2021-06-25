# Integrate Amazon EMR with AWS Lake Formation<a name="emr-lake-formation"></a>

Beginning with Amazon EMR 5\.31\.0, you can launch a cluster that integrates with AWS Lake Formation\. AWS Lake Formation is a managed service that helps you discover, catalog, cleanse, and secure data in an Amazon Simple Storage Service \(Amazon S3\) data lake\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.

This section provides a conceptual overview of Amazon EMR integration with Lake Formation\. It also lists the prerequisites and steps required to launch an Amazon EMR cluster integrated with Lake Formation\. 

Integrating Amazon EMR with AWS Lake Formation provides the following key benefits:
+ Fine\-grained, column\-level access to databases and tables in the AWS Glue Data Catalog\. 
+ Federated single sign\-on to EMR Notebooks or Apache Zeppelin from enterprise identity systems compatible with Security Assertion Markup Language \(SAML\) 2\.0\. 

**Important**  
If you currently use EMR clusters with Lake Formation in beta mode, you should upgrade your clusters to EMR version 5\.31\.0 or above to continue using this feature\. Clusters with an EMR version below 5\.31\.0 will stop working with Lake Formation\.

**Note**  
EMR integration with Lake Formation is not yet available for the EMR 6\.x series and does not currently support using AWS Single Sign\-On for federated single sign\-on\.

**Topics**
+ [Overview of Amazon EMR integration with Lake Formation](emr-lf-conceptual.md)
+ [Applications, features, and limitations](emr-lf-scope.md)
+ [Before you begin](emr-lf-prerequisites.md)
+ [Launch an Amazon EMR cluster with Lake Formation](emr-lf-launch.md)