# Overview of Amazon EMR integration with Lake Formation<a name="emr-lf-conceptual"></a>

When you integrate Amazon EMR with AWS Lake Formation, you enable SAML\-based authentication using corporate credentials for EMR Notebooks and Apache Zeppelin, and can enforce fine\-grained, column\-level access control to data lakes based on policies you define in AWS Lake Formation\.

## Requirements<a name="emr-lf-conceptual-requirements"></a>

Your organization must meet the following requirements before you integrate Amazon EMR and Lake Formation:
+ Manage your corporate identities with an existing, SAML\-based identity provider \(IdP\) such as Active Directory Federation Services \(AD FS\)\. For more information, see [Configure third\-party providers for SAML](emr-lf-idp.md)\.
+ Use the AWS Glue Data Catalog as a metadata store\.
+ Define and manage permissions in Lake Formation to access databases, tables, and columns in AWS Glue Data Catalog\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.
+ Use EMR Notebooks or Apache Zeppelin to access data managed by AWS Glue and Lake Formation\. 

## Integration steps<a name="emr-lf-conceptual-integration"></a>

To integrate Amazon EMR with Lake Formation, you will need to take the following steps:

1. Complete prerequisite tasks including configuring your identity provider, creating IAM roles for Lake Formation, setting up a security configuration, and preparing Lake Formation resources\. For more information about prerequisites for Amazon EMR and Lake Formation integration, see [Before you begin](emr-lf-prerequisites.md)\.

1. Launch a cluster with the new roles and security configuration you created for Lake Formation\. For more information, see [Launch an Amazon EMR cluster with Lake Formation](emr-lf-launch-cluster.md)\.

1. Update the callback or Single Sign\-on URL with your IdP to redirect users after successful SAML authentication to the master node of your cluster\. For more information, see [Update the callback or single sign\-on URL with your Identity Provider](emr-lf-launch-cluster.md#emr-lf-url)\.

## How data access works in Lake Formation<a name="emr-lf-data"></a>

After you integrate Amazon EMR with Lake Formation, users authenticate through your organization's Identity Provider \(IdP\) sign\-on page to access EMR Notebooks or Zeppelin\. Then, Lake Formation provides access to data through temporary credentials to EMR\. This process is known as credential vending\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\.

Following is a high\-level overview of how EMR gets access to data protected by Lake Formation security policies:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/lake_formation_emr_security_aws.png)

1. A user submits a query in Amazon EMR on data from Lake Formation\.

1. Amazon EMR requests temporary credentials from AWS Lake Formation for access for that user\.

1. Lake Formation returns temporary credentials, allowing data access\. 

1. Amazon EMR sends the query request to retrieve data from Amazon S3\.

1. Amazon EMR receives the data from Amazon S3, then filters and returns the results based on the user permissions that you defined in Lake Formation\. 

For detailed information about how user authentication and access to data works, see [Amazon EMR components](emr-lf-components.md) and [Architecture of SAML\-enabled single sign\-on and fine\-grained access control](emr-lf-architecture.md)\. 

For more information about adding users and groups to Lake Formation policies, see [Granting Data Catalog permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/granting-catalog-permissions.html)\.

**Topics**
+ [Requirements](#emr-lf-conceptual-requirements)
+ [Integration steps](#emr-lf-conceptual-integration)
+ [How data access works in Lake Formation](#emr-lf-data)
+ [IAM roles for Lake Formation](emr-lf-iam-role.md)
+ [Terms and concepts](emr-lf-terms.md)
+ [Amazon EMR components](emr-lf-components.md)
+ [Architecture of SAML\-enabled single sign\-on and fine\-grained access control](emr-lf-architecture.md)