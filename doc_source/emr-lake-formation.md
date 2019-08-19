# Integrating Amazon EMR with AWS Lake Formation \(Beta\)<a name="emr-lake-formation"></a>

Beginning with Amazon EMR 5\.26\.0, you can launch a cluster that integrates with AWS Lake Formation\. This feature is available in public beta\. 

AWS Lake Formation is a managed service that simplifies setting up, securing, and managing data lakes\. AWS Lake Formation helps you discover, catalog, cleanse, and secure data in an Amazon Simple Storage Service \(Amazon S3\) data lake\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.

Integrating Amazon EMR with AWS Lake Formation provides the following key benefits:
+ Provides fine\-grained, column\-level access to databases and tables in the AWS Glue Data Catalog\. 
+ Enables federated single sign\-on to EMR Notebooks or Apache Zeppelin from your enterprise identity system that is compatible with Security Assertion Markup Language \(SAML\) 2\.0\. 

This section provides a conceptual overview of Amazon EMR integration with Lake Formation\. It also provides the prerequisites and steps required to launch an Amazon EMR cluster integrated with Lake Formation\. 

**Topics**
+ [Conceptual Overview of Amazon EMR Integration with Lake Formation](emr-lf-conceptual.md)
+ [Supported Applications and Features](emr-lf-scope.md)
+ [Before You Begin](emr-lf-prerequisites.md)
+ [Launch an Amazon EMR Cluster with Lake Formation](emr-lf-launch.md)