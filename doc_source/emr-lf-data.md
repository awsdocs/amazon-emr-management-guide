# How Access to Data Works in Lake Formation<a name="emr-lf-data"></a>

Lake Formation allows access to data by providing temporary credentials to services such as Amazon EMR\. This process is known as credential vending\. For more information, see [AWS Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/what-is-lake-formation.html)\. 

When you run a query on data that is protected by Lake Formation security policies, Amazon EMR requests temporary credentials from AWS Lake Formation to access data stored in Amazon S3\.

Here is how access to data is granted:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/lake_formation_emr_security_aws.png)

1. You set up and control user access to resources by using AWS Lake Formation policies\. You can create the policies by using a set of grant and revoke permissions available to you within the Lake Formation Console\. For example, you can grant access to a database or a table\. You can also grant column\-level permissions to users\. You specify permissions for tables and columns directly in Lake Formation, instead of specifying them for Amazon S3 buckets and objects\. For more information, see [Lake Formation Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/security-data-access.html#security-data-access-permissions)\.

1. When a principal attempts to run a query in Amazon EMR on data from Lake Formation, Amazon EMR requests temporary credentials for data access from AWS Lake Formation\.

1. Lake Formation returns temporary credentials, allowing data access\. 

1. Amazon EMR sends the query request to obtain data from Amazon S3\.

1. Amazon EMR filters and returns the results based on the user permissions defined in Lake Formation\. 