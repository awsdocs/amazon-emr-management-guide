# Apache Hive plugin<a name="emr-ranger-hive"></a>

Apache Hive is a popular execution engine within the Hadoop ecosystem\. Amazon EMR provides an Apache Ranger plugin to be able to provide fine\-grained access controls for Hive\. The plugin is compatible with open source Apache Ranger Admin server version 2\.0 and later\.

## Supported features<a name="emr-ranger-supported-features"></a>

The Apache Ranger plugin for Hive on EMR supports all the functionality of the open source plugin, which includes database, table, column level access controls and row filtering and data masking\. For a table of Hive commands and associated Ranger permissions, see [Hive commands to Ranger permission mapping](https://cwiki.apache.org/confluence/display/RANGER/Hive+Commands+to+Ranger+Permission+Mapping)\.

## Installation of service configuration<a name="emr-ranger-hive-service-config"></a>

The Apache Hive plugin is compatible with the existing Hive service definition within Apache Hive "Hadoop SQL" as shown below\.

![\[Apache Hive service definition for Hadoop SQL.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger_service_mgr.png)

If you do not have an instance of the service under Hadoop SQL, like shown above, you can create one\. Click on the **\+** next to Hadoop SQL\.

1. **Service Name \(If displayed\)**: Enter the service name\. The suggested value is **amazonemrhive**\. Make a note of this service name \-\- it's needed when creating an EMR security configuration\.

1. **Display Name**: Enter the name to be displayed for the service\. The suggested value is **amazonemrhive**\.

![\[Apache Hive service details for Hadoop SQL.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger_create_service.png)

The Apache Hive Config Properties are used to establish a connection to your Apache Ranger Admin server with a HiveServer2 to implement auto complete when creating policies\. The properties below are not required to be accurate if you do not have a persistent HiveServer2 process and can be filled with any information\.
+ **Username**: Enter a user name for the JDBC connection to an instance of an HiveServer2 instance\.
+ **Password**: Enter the password for the user name above\.
+ **jdbc\.driver\.ClassName**: Enter the class name of JDBC class for Apache Hive connectivity\. The default value can be used\.
+ **jdbc\.url**: Enter the JDBC connection string to use when connecting to HiveServer2\.
+ **Common Name for Certificate**: The CN field within the certificate used to connect to the admin server from a client plugin\. This value must match the CN field in your TLS certificate that was created for the plugin\.

![\[Apache Hive service configuration properties.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger_config_props.png)

The **Test Connection** button tests whether the values above can be used to successfully connect to the HiveServer2 instance\. Once the service is successfully created, the Service Manager should look like below:

![\[Connected to the HiveServer2 instance\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger_config_connected.png)

## Other considerations<a name="emr-ranger-hive-considerations"></a>

**Hive Metadata Server**

The Hive Metadata server can only be accessed by the trusted engines, specifically the Hive and emr\_record\_server to protect from unauthorized access\. The Hive metadata server is also accessed by all nodes on the cluster and required port 9083 is accessible by all nodes to the master node\.

**Authentication**

By default, Apache Hive is configured to authenticate using Kerberos as configured in the EMR Security configuration\. HiveServer2 can be configured to authenticate users using LDAP as well\. See [Implementing LDAP authentication for Hive on a multi\-tenant Amazon EMR cluster](https://aws.amazon.com/blogs/big-data/implementing-ldap-authentication-for-hive-on-a-multi-tenant-amazon-emr-cluster/) for information\.

## Limitations<a name="emr-ranger-hive-limitations"></a>

The following are current limitations for the Apache Hive plugin on Amazon EMR 5\.x:
+ Hive roles are not currently supported\. Grant, Revoke statements are not supported\.
+ Hive CLI is not supported\. JDBC/Beeline is the only authorized way to connect Hive\.
+ `hive.server2.builtin.udf.blacklist` configuration should be populated with UDFs that you deem unsafe\.