# Service definition installation<a name="emr-ranger-admin-servicedef-install"></a>

A service definition is used by the Ranger Admin server to describe the attributes of policies for an application\. The policies are then stored in a policy repository for clients to download\. 

To be able to configure service definitions, REST calls must be made to the Ranger Admin server\. See [Apache Ranger PublicAPIsv2](https://ranger.apache.org/apidocs/resource_PublicAPIsv2.html#resource_PublicAPIsv2_createServiceDef_POST)for APIs required in the following section\.

**Installing Apache Spark's Service Definition**

To install Apache Spark's service definition, see [Apache Spark plugin](emr-ranger-spark.md)\.

**Installing EMRFS Service Definition**

To install the S3 service definition for Amazon EMR, see [EMRFS S3 plugin](emr-ranger-emrfs.md)\.

**Using Hive Service Definition**

Apache Hive can use the existing Ranger service definition that ships with Apache Ranger 2\.0 and later\. For more information, see [Apache Hive plugin](emr-ranger-hive.md)\.