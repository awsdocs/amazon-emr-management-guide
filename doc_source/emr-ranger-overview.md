# Apache Ranger<a name="emr-ranger-overview"></a>

Apache Ranger is a framework to enable, monitor, and manage comprehensive data security across the Hadoop platform\.

Apache Ranger has the following features:
+ Centralized security administration to manage all security related tasks in a central UI or using REST APIs\.
+ Fine\-grained authorization to do a specific action or operation with a Hadoop component or tool, managed through a central administration tool\.
+ A standardized authorization method across all Hadoop components\.
+ Enhanced support for various authorization methods\.
+ Centralized auditing of user access and administrative actions \(security related\) within all the components of Hadoop\.

Apache Ranger uses two key components for authorization: 
+ **Apache Ranger policy admin server**, which allows you to define the authorization policies for Hadoop applications\. When you integrate Apache Ranger with Amazon EMR, you can define and enforce policies for Apache Spark and Hive to access Hive Metastore or to access Amazon S3 data using the EMR File System\. See [Use EMR File System \(EMRFS\)](emr-fs.md)\. You can set up a new or use an existing Apache Ranger policy admin server to integrate with Amazon EMR\.
+ **Apache Ranger plugin**, which validates the access of a user against the authorization policies defined in Apache Ranger policy admin server\. Amazon EMR installs and configures Apache Ranger plugin automatically for each Hadoop application selected in the Apache Ranger configuration\. 
+ **Apache Ranger policy admin server** \- This server allows you to define the authorization policies for Hadoop applications\. When integrating with Amazon EMR, you are able to define and enforce policies for Apache Spark and Hive to access Hive Metastore, and accessing Amazon S3 data [using the EMR File System](emr-fs.md)\. You can set up a new or use an existing Apache Ranger policy admin server to integrate with Amazon EMR\.
+ **Apache Ranger plugin** \- This plugin validates the access of a user against the authorization policies defined in the Apache Ranger policy admin server\. Amazon EMR installs and configures the Apache Ranger plugin automatically for each Hadoop application selected in the Apache Ranger configuration\. 

**Topics**
+ [Architecture of Amazon EMR integration with Apache Ranger](emr-ranger-architecture.md)
+ [Amazon EMR Components](emr-ranger-components.md)