# Apache Spark plugin<a name="emr-ranger-spark"></a>

Amazon EMR has integrated EMR RecordServer to provide fine\-grained access control for SparkSQL\. EMR's RecordServer is a privileged process running on all nodes on an Apache Ranger\-enabled cluster\. When a Spark driver or executor runs a SparkSQL statement, all metadata and data requests go through the RecordServer\. To learn more about EMR RecordServer, see the [Amazon EMR components](emr-ranger-components.md) page\.

**Topics**
+ [Supported features](#emr-ranger-spark-supported-features)
+ [Redeploy service definition to use INSERT INTO, INSERT OVERWRITE, or ALTER TABLE statements](#emr-ranger-spark-redeploy-service-definition)
+ [Installation of service definition](#emr-ranger-spark-install-servicedef)
+ [Creating SparkSQL policies](#emr-ranger-spark-create-sparksql)
+ [Considerations](#emr-ranger-spark-considerations)
+ [Limitations](#emr-ranger-spark-limitations)

## Supported features<a name="emr-ranger-spark-supported-features"></a>


| SQL statement/Ranger action | STATUS | Supported EMR release | 
| --- | --- | --- | 
|  SELECT  |  Supported  |  As of 5\.32  | 
|  SHOW DATABASES  |  Supported  |  As of 5\.32  | 
|  SHOW TABLES  |  Supported  |  As of 5\.32  | 
|  SHOW COLUMNS  |  Supported  |  As of 5\.32  | 
|  SHOW TABLE PROPERTIES  |  Supported  |  As of 5\.32  | 
|  DESCRIBE TABLE  |  Supported  |  As of 5\.32  | 
|  CREATE TABLE  |  Not Supported  |    | 
|  UPDATE TABLE  |  Not Supported  |    | 
|  INSERT OVERWRITE  |  Supported  |  As of 5\.34 and 6\.4  | 
| INSERT INTO | Supported | As of 5\.34 and 6\.4 | 
|  ALTER TABLE  |  Supported  |  As of 6\.4  | 
|  DELETE FROM  |  Not Supported  |    | 

The following features are supported when using SparkSQL:
+ Fine\-grained access control on tables within the Hive Metastore, and policies can be created at a database, table, and column level\.
+ Apache Ranger policies can include grant policies and deny policies to users and groups\.
+ Audit events are submitted to CloudWatch Logs\.

## Redeploy service definition to use INSERT INTO, INSERT OVERWRITE, or ALTER TABLE statements<a name="emr-ranger-spark-redeploy-service-definition"></a>

**Note**  
Starting with Amazon EMR 6\.4, you can use Spark SQL with the statements: INSERT INTO, INSERT OVERWRITE, or ALTER TABLE\. If you have an existing installation on Apache Ranger server with Apache Spark service definitions deployed, use the following code to redeploy the service definitions\.  

```
# Get existing Spark service definition id calling Ranger REST API and JSON processor
curl --silent -f -u <admin_user_login>:<password_for_ranger_admin_user> \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-k 'https://*<RANGER SERVER ADDRESS>*:6182/service/public/v2/api/servicedef/name/amazon-emr-spark' | jq .id

# Download the latest Service definition
wget https://s3.amazonaws.com/elasticmapreduce/ranger/service-definitions/version-2.0/ranger-servicedef-amazon-emr-spark.json

# Update the service definition using the Ranger REST API
curl -u <admin_user_login>:<password_for_ranger_admin_user> -X PUT -d @ranger-servicedef-amazon-emr-spark.json \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-k 'https://*<RANGER SERVER ADDRESS>*:6182/service/public/v2/api/servicedef/<Spark service definition id from step 1>'
```

## Installation of service definition<a name="emr-ranger-spark-install-servicedef"></a>

The installation of EMR's Apache Spark service definition requires the Ranger Admin server to be setup\. See [Set up Ranger Admin server](emr-ranger-admin.md)\.

Follow these steps to install the Apache Spark service definition:

**Step 1: SSH into the Apache Ranger Admin server**

For example:

```
ssh ec2-user@ip-xxx-xxx-xxx-xxx.ec2.internal
```

**Step 2: Download the service definition and Apache Ranger Admin server plugin**

In a temporary directory, download the service definition\. This service definition is supported by Ranger 2\.x versions\.

```
mkdir /tmp/emr-spark-plugin/
cd /tmp/emr-spark-plugin/

wget https://s3.amazonaws.com/elasticmapreduce/ranger/service-definitions/version-2.0/ranger-spark-plugin-2.x.jar
wget https://s3.amazonaws.com/elasticmapreduce/ranger/service-definitions/version-2.0/ranger-servicedef-amazon-emr-spark.json
```

**Step 3: Install the Apache Spark plugin for Amazon EMR**

```
export RANGER_HOME=.. # Replace this Ranger Admin's home directory eg /usr/lib/ranger/ranger-2.0.0-admin
mkdir $RANGER_HOME/ews/webapp/WEB-INF/classes/ranger-plugins/amazon-emr-spark
mv ranger-spark-plugin-2.x.jar $RANGER_HOME/ews/webapp/WEB-INF/classes/ranger-plugins/amazon-emr-spark
```

**Step 4: Register the Apache Spark service definition for Amazon EMR**

```
curl -u *<admin users login>*:*_<_**_password_ **_for_** _ranger admin user_**_>_* -X POST -d @ranger-servicedef-amazon-emr-spark.json \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-k 'https://*<RANGER SERVER ADDRESS>*:6182/service/public/v2/api/servicedef'
```

If this command runs successfully, you see a new service in your Ranger Admin UI called "AMAZON\-EMR\-SPARK", as shown in the following image \(Ranger version 2\.0 is shown\)\.

![\["AMAZON-EMR-SPARK" registered in Ranger Admin.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-amazon-emr-spark.png)

**Step 5: Create an instance of the AMAZON\-EMR\-SPARK application**

**Service Name \(If displayed\):** The service name that will be used\. The suggested value is **amazonemrspark**\. Note this service name as it will be needed when creating an EMR security configuration\.

**Display Name:** The name to be displayed for this instance\. The suggested value is **amazonemrspark**\.

**Common Name For Certificate:** The CN field within the certificate used to connect to the admin server from a client plugin\. This value must match the CN field in your TLS certificate that was created for the plugin\.

![\[Ranger Admin create service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-service.png)

**Note**  
The TLS certificate for this plugin should have been registered in the trust store on the Ranger Admin server\. See [TLS certificates](emr-ranger-admin-tls.md) for more details\.

## Creating SparkSQL policies<a name="emr-ranger-spark-create-sparksql"></a>

When creating a new policy, the fields to fill in are:

**Policy Name**: The name of this policy\.

**Policy Label**: A label that you can put on this policy\.

**Database**: The database that this policy applies to\. The wildcard "\*" represents all columns\.

**Table**: The tables that this policy applies to\. The wildcard "\*" represents all columns\.

**EMR Spark Column**: The columns that this policy applies to\. The wildcard "\*" represents all columns\.

**Description**: A description of this policy\.

![\[Ranger Admin create SparkSQL policy details.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-policy-details.png)

To specify the users and groups, enter the users and groups below to grant permissions\. EMR SparkSQL currently only supports the **select** action\. You can also specify exclusions for the **allow** conditions and **deny** conditions shown below\.

![\[Ranger Admin SparkSQL policy details allow conditions.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-policy-allow-conditions.png)

After specifying the allow and deny conditions, click **Save**\.

## Considerations<a name="emr-ranger-spark-considerations"></a>

Each node within the EMR cluster must be able to connect to the master node on port 9083\.

## Limitations<a name="emr-ranger-spark-limitations"></a>

The following are current limitations for the Apache Spark plugin:
+ Record Server will always connect to HMS running on an Amazon EMR cluster\. Configure HMS to connect to Remote Mode, if required\. You should not put config values inside the Apache Spark Hive\-site\.xml configuration file\.
+ Tables created using Spark datasources on CSV or Avro are not readable using EMR RecordServer\. Use Hive to create and write data, and read using Record\.
+ Delta Lake and Hudi tables are not supported\.
+ Users must have access to the default database\. This is a requirement for Apache Spark\.
+ Ranger Admin server does not support auto\-complete\.
+ The SparkSQL plugin for Amazon EMR does not support row filters or data masking\.
+ When using ALTER TABLE with Spark SQL, a partition location must be the child directory of a table location\. Inserting data into a partition where the partition location is different from the table location is not supported\.
+ We support only the following ALTER TABLE statements: 
  + [ADD COLUMN](https://spark.apache.org/docs/latest/sql-ref-syntax-ddl-alter-table.html#add-columns)
  + [ADD PARTITION](https://spark.apache.org/docs/latest/sql-ref-syntax-ddl-alter-table.html#add-partition)
  + [SET TABLE PROPERTIES](https://spark.apache.org/docs/latest/sql-ref-syntax-ddl-alter-table.html#set-table-properties)
  + [SET LOCATION](https://spark.apache.org/docs/latest/sql-ref-syntax-ddl-alter-table.html#set-location-and-set-file-format)
  + [UNSET](https://spark.apache.org/docs/latest/sql-ref-syntax-ddl-alter-table.html#set-and-unset)