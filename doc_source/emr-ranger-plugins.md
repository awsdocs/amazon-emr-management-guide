# Apache Ranger Plugins<a name="emr-ranger-plugins"></a>

## Apache Hive Plugin<a name="emr-ranger-hive"></a>

Apache Hive is a popular execution engine within the Hadoop ecosystem\. Amazon EMR provides an Apache Ranger plugin to be able to provide fine\-grained access controls for Hive\. The plugin is compatible with open source Apache Ranger Admin server version 2\.0 and later\.

### Supported Features<a name="emr-ranger-supported-features"></a>

The Apache Ranger plugin for Hive on EMR supports all the functionality of the open source plugin, which includes database, table, column level access controls and row filtering and data masking\. For a table of Hive commands and associated Ranger permissions, see [Hive Commands to Ranger Permission Mapping](https://cwiki.apache.org/confluence/display/RANGER/Hive+Commands+to+Ranger+Permission+Mapping)\.

### Installation of Service Configuration<a name="emr-ranger-hive-service-config"></a>

The Apache Hive plugin is compatible with the existing Hive service definition within Apache Hive “Hadoop SQL“ as shown below\.

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

### Other Considerations<a name="emr-ranger-hive-considerations"></a>

**Hive Metadata Server**

The Hive Metadata server can only be accessed by the trusted engines, specifically the Hive and emr\_record\_server to protect from unauthorized access\. The Hive metadata server is also accessed by all nodes on the cluster and required port 9083 is accessible by all nodes to the master node\.

**Authentication**

By default, Apache Hive is configured to authenticate using Kerberos as configured in the EMR Security configuration\. HiveServer2 can be configured to authenticate users using LDAP as well\. See [Implementing LDAP authentication for Hive on a multi\-tenant Amazon EMR cluster](https://aws.amazon.com/blogs/big-data/implementing-ldap-authentication-for-hive-on-a-multi-tenant-amazon-emr-cluster/) for information\.

### Limitations<a name="emr-ranger-hive-limitations"></a>

The following are a list of limitations for the Apache Hive Plugin on Amazon EMR 5\.x:
+ Hive roles are not currently supported\. Grant, Revoke statements are not supported\.
+ Hive CLI is not supported\. JDBC/Beeline is the only authorized way to connect Hive\.
+ `hive.server2.builtin.udf.blacklist` configuration should be populated with UDFs that you deem unsafe\.

## Apache Spark Plugin<a name="emr-ranger-spark"></a>

Amazon EMR has integrated EMR RecordServer to be able to provide fine\-grained access control for SparkSQL as well as EMRFS S3 Ranger Plugin to provide course grained access control\. EMR’s RecordServer is a privileged process running on all nodes on an Apache Ranger enabled cluster\. When a Spark driver or executor runs a SparkSQL statement, all metadata and data requests go through the RecordServer\.

### Supported Features<a name="emr-ranger-spark-supported-features"></a>


| SQL statement/Ranger Action | SELECT | Supported EMR Release | 
| --- | --- | --- | 
|  SELECT  |  Supported  |  As of 5\.32  | 
|  SHOW DATABASES  |  Supported  |  As of 5\.32  | 
|  SHOW TABLES  |  Supported  |  As of 5\.32  | 
|  SHOW COLUMNS  |  Supported  |  As of 5\.32  | 
|  SHOW TABLE PROPERTIES  |  Supported  |  As of 5\.32  | 
|  DESCRIBE TABLE  |  Supported  |  As of 5\.32  | 
|  CREATE TABLE  |  Not Supported  |    | 
|  UPDATE TABLE  |  Not Supported  |    | 
|  INSERT INTO/OVERWRITE  |  Not Supported  |    | 
|  ALTER TABLE  |  Not Supported  |    | 
|  DELETE FROM  |  Not Supported  |    | 

The following features are supported when using SparkSQL:
+ Fine\-grained access control on tables within the Hive Metastore, and policies can be created at a database, table, and column level\.
+ Apache Ranger policies can include grant policies and deny policies to users and groups\.
+ Audit events are submitted to CloudWatch Logs\.

### Installation of Service Definition<a name="emr-ranger-spark-install-servicedef"></a>

The installation of EMR’s Apache Spark service definition requires the Ranger Admin server to be setup\. See [Set Up Ranger Admin Server](emr-ranger-admin.md)\.

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

If this command runs successfully, you see a new service in your Ranger Admin UI called “AMAZON\-EMR\-SPARK”, as shown in the following image \(Ranger version 2\.0 is shown\)\.

![\[“AMAZON-EMR-SPARK” registered in Ranger Admin.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-amazon-emr-spark.png)

**Step 5: Create an instance of the AMAZON\-EMR\-SPARK application**

**Service Name \(If displayed\):** The service name that will be used\. The suggested value is **amazonemrspark**\. Note this service name as it will be needed when creating an EMR security configuration\.

**Display Name:** The name to be displayed for this instance\. The suggested value is **amazonemrspark**\.

**Common Name For Certificate:** The CN field within the certificate used to connect to the admin server from a client plugin\. This value must match the CN field in your TLS certificate that was created for the plugin\.

![\[Ranger Admin create service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-service.png)

**Note**  
The TLS certificate for this plugin should have been registered in the trust store on the Ranger Admin server\. See [TLS Certificates](emr-ranger-admin-tls.md) for more details\.

### Creating SparkSQL Policies<a name="emr-ranger-spark-create-sparksql"></a>

When creating a new policy, the fields to fill in are:

**Policy Name**: The name of this policy\.

**Policy Label**: A label that you can put on this policy\.

**Database**: The database that this policy applies to\. The wildcard “\*” represents all columns\.

**Table**: The tables that this policy applies to\. The wildcard “\*” represents all columns\.

**EMR Spark Column**: The columns that this policy applies to\. The wildcard “\*” represents all columns\.

**Description**: A description of this policy\.

![\[Ranger Admin create SparkSQL policy details.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-policy-details.png)

To specify the users and groups, enter the users and groups below to grant permissions\. EMR SparkSQL currently only supports the **select** action\. You can also specify exclusions for the **allow** conditions and **deny** conditions shown below\.

![\[Ranger Admin SparkSQL policy details allow conditions.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-policy-allow-conditions.png)

After specifying the allow and deny conditions, click **Save**\.

### Additional Considerations<a name="emr-ranger-spark-considerations"></a>

Each node within the EMR cluster must be able to connect to the master node on port 9083\.

### Limitations<a name="emr-ranger-spark-limitations"></a>

The following are current limitations:
+ Write actions within SparkSQL, such as INSERT and ALTER TABLE statements, are not supported\.
+ Record Server will always connect to HMS running on an Amazon EMR cluster\. Configure HMS to connect to Remote Mode, if required\. You should not put config values inside the Apache Spark hive\-site\.xml configuration file\.
+ Tables created using Spark datasources on CSV or Avro are not readable using EMR RecordServer\. Use Hive to create and write data, and read using Record\.
+ Delta Lake and Hudi tables are not supported\.
+ Users must have access to the default database\. This is a requirement for Apache Spark\.
+ Ranger Admin server does not support auto\-complete\.
+ The SparkSQL plugin for Amazon EMR does not support row filters or data masking\.

## EMRFS S3 Plugin<a name="emr-ranger-emrfs"></a>

To make it easier to provide access controls against objects in S3 on a multi\-tenant cluster, the EMRFS S3 Plugin provides access controls to the data within S3 when accessing it through EMRFS\. You can allow access to S3 resources at a user and group level\.

To achieve this, when your application attempts to access data within S3, EMRFS sends a request for credentials to the Secret Agent process, where the request is authenticated and authorized against an Apache Ranger plugin\. If the request is authorized, then the Secret Agent assumes the IAM role for Apache Ranger Engines with a restricted policy to generate credentials that only have access to the Ranger policy that allowed the access\. The credentials are then passed back to EMRFS to access S3\.

### Supported Features<a name="emr-ranger-emrfs-features"></a>

EMRFS S3 Plugin provides storage level authorization\. Policies can be created to provide access to users and groups to S3 buckets and prefixes\. Authorization is done only against EMRFS\.

### Installation of Service Configuration<a name="emr-ranger-emrfs-service-config"></a>

The installation of the EMRFS S3 service definition requires that the Ranger Admin server to be setup\. See [Set Up Ranger Admin Server](emr-ranger-admin.md)\.

Follow these steps to install the EMRFS service definition\.

**Step 1: SSH into the Apache Ranger Admin server**\.

For example:

```
ssh ec2-user@ip-xxx-xxx-xxx-xxx.ec2.internal
```

**Step 2: Download the Amazon EMR service definition and Apache Ranger Admin server plugin**\.

In a temporary directory, download the Amazon EMR service definition\. This service definition is supported by Ranger 2\.x versions\.

```
mkdir /tmp/emr-emrfs-plugin/
cd /tmp/emr-emrfs-plugin/

wget https://s3.amazonaws.com/elasticmapreduce/ranger/service-definitions/version-2.0/ranger-servicedef-amazon-emr-emrfs.json
wget https://s3.amazonaws.com/elasticmapreduce/ranger/service-definitions/version-2.0/ranger-emr-emrfs-plugin-2.x.jar
```

**Step 3: Install the Apache EMRFS S3 plugin for Amazon EMR**\.

```
export RANGER_HOME=.. # Replace this Ranger Admin's home directory eg /usr/lib/ranger/ranger-2.0.0-admin
mkdir $RANGER_HOME/ews/webapp/WEB-INF/classes/ranger-plugins/amazon-emr-emrfs
mv ranger-emr-emrfs-plugin-2.x.jar $RANGER_HOME/ews/webapp/WEB-INF/classes/ranger-plugins/amazon-emr-emrfs
```

**Step 4: Register EMRFS S3 service definition**\.

```
curl -u *<admin users login>*:*_<_**_password_ **_for_** _ranger admin user_**_>_* -X POST -d @ranger-servicedef-amazon-emr-emrfs.json \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-k 'https://*<RANGER SERVER ADDRESS>*:6182/service/public/v2/api/servicedef'
```

If this command runs successfully, you see a new service in the Ranger Admin UI called “AMAZON\-EMR\-S3”, as shown in the following image \(Ranger version 2\.0 is shown\)\.

![\[Ranger Admin create EMRFS S3 service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-service-EMRFS.png)

**Step 5: Create an instance of the AMAZON\-EMR\-EMRFS application**\.

Create an instance of the service definition\.
+ Click on the **\+** next to AMAZON\-EMR\-EMRFS\.

Fill in the following fields:

**Service Name \(If displayed\)**: The suggested value is **amazonemrspark**\. Note this service name as it will be needed when creating an EMR security configuration\. 

**Display Name**: The name displayed for this service\. The suggested value is **amazonemrspark**\.

**Common Name For Certificate**: The CN field within the certificate used to connect to the admin server from a client plugin\. This value must match the CN field in the TLS certificate that was created for the Plugin\.

![\[Ranger Admin edit EMRFS S3 service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-edit-service-EMRFS.png)

**Note**  
The TLS certificate for this plugin should have been registered in the trust store on the Ranger Admin server\. See [TLS Certificates](emr-ranger-admin-tls.md) for more details\.

When the service is created, the Service Manager includes “AMAZON\-EMR\-EMRFS”, as shown in the following image\.

![\[Ranger Admin showing new EMRFS S3 service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-new-service-EMRFS.png)

### Creating EMRFS S3 Policies<a name="emr-ranger-emrfs-create-policies"></a>

To create a new policy, in the Create Policy page of the Service Manager, fill in the following fields\.

**Policy Name**: The name of this policy\.

**Policy Label**: A label that you can put on this policy\.

**S3 Resource**: A resource starting with the bucket and optional prefix\. See [EMRFS S3 Policies Usage Notes](#emr-ranger-emrfs-considerations) for information on best practices\. Resources in Ranger Admin server should not contain **s3://**, **s3a://** or **s3n://**\.

![\[Ranger Admin showing create policy for EMRFS S3 service.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-create-policy-EMRFS.png)

As shown below, you can specify users and groups to grant permissions\. EMR SparkSQL currently only supports the **select** action\. You can also specify exclusions for the allow conditions and deny conditions shown below\.

![\[Ranger Admin showing user/group permissions for EMRFS S3 policy.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-permissions-EMRFS.png)

**Note**  
A maximum of three resources are allowed for each policy\. Adding more than three resources may result in an error when this policy is used on an EMR cluster\. Adding more than three policies displays a reminder about the policy limit\.

### EMRFS S3 Policies Usage Notes<a name="emr-ranger-emrfs-considerations"></a>

When creating S3 policies within Apache Ranger, there are some usage considerations to be aware of\.

#### Permissions to Multiple S3 Objects<a name="emr-ranger-emrfs-considerations-s3objects"></a>

You can use recursive policies and wildcard expressions to give permissions to multiple S3 objects with common prefixes\. Recursive policies give permissions to all objects with a common prefix\. Wildcard expressions select multiple prefixes\. Together, they give permissions to all objects with multiple common prefixes as shown in the following examples\.

**Example Using a Recursive Policy**  
Suppose you want permissions to list all the parquet files in an S3 bucket organized as follows\.  

```
s3://sales-reports/americas/
    +- year=2000
    |      +- data-q1.parquet
    |      +- data-q2.parquet
    +- year=2019
    |      +- data-q1.json
    |      +- data-q2.json
    |      +- data-q3.json
    |      +- data-q4.json
    |
    +- year=2020
    |      +- data-q1.parquet
    |      +- data-q2.parquet
    |      +- data-q3.parquet
    |      +- data-q4.parquet
    |      +- annual-summary.parquet    
    +- year=2021
```
First, consider the parquet files with the prefix `s3://sales-reports/americas/year=2000`\. You can grant GetObject permissions to all of them in two ways:  
**Using non\-recursive policies**: One option is to use two separate non\-recursive policies, one for the directory and the other for the files\.   
The first policy grants permission to the prefix `s3://sales-reports/americas/year=2020` \(there is no trailing `/`\)\.  

```
- S3 resource = "sales-reports/americas/year=2000"
- permission = "GetObject"
- user = "analyst"
```
The second policy uses wildcard expression to grant permissions all the files with prefix `sales-reports/americas/year=2020/` \(note the trailing `/`\)\.  

```
- S3 resource = "sales-reports/americas/year=2020/*"
- permission = "GetObject"
- user = "analyst"
```
**Using a recursive policy**: A more convenient alternative is to use a single recursive policy and grant recursive permission to the prefix\.  

```
 - S3 resource = "sales-reports/americas/year=2020"
 - permission = "GetObject"
 - user = "analyst"
 - is recursive = "True"
```
So far, only the parquet files with the prefix `s3://sales-reports/americas/year=2000` have been included\. You can now also include the parquet files with a different prefix, `s3://sales-reports/americas/year=2020`, into the same recursive policy by introducing a wildcard expression as follows\.  

```
 - S3 resource = "sales-reports/americas/year=20?0"
 - permission = "GetObject"
 - user = "analyst"
 - is recursive = "True"
```

#### Policies for PutObject and DeleteObject Permissions<a name="emr-ranger-emrfs-considerations-putobject"></a>

Writing policies for `PutObject` and `DeleteObject` permissions to files on EMRFS need special care because, unlike GetObject permissions, they need additional recursive permissions granted to the prefix\.

**Example Policies for PutObject and DeleteObject Permissions**  
For example, deleting the file `annual-summary.parquet` requires not only a DeleteObject permission to the actual file\.  

```
- S3 resource = "sales-reports/americas/year=2020/annual-summary.parquet"
- permission = "DeleteObject"
- user = "analyst"
```
It also requires a policy granting recursive `GetObject` and `PutObject` permissions to its prefix\.  
Similarly, modifying the file `annual-summary.parquet`, requires not only a `PutObject` permission to the actual file\.  

```
- S3 resource = "sales-reports/americas/year=2020/annual-summary.parquet"
- permission = "PutObject"
- user = "analyst"
```
It also requires a policy granting recursive `GetObject` permission to its prefix\.  

```
- S3 resource = "sales-reports/americas/year=2020"
- permission = "GetObject"
- user = "analyst"
- is recursive = "True"
```

#### Wildcards in Policies<a name="emr-ranger-emrfs-considerations-wildcards"></a>

There are two areas in which wildcards can be specified\. When specifying an S3 resource, the “\*” and “?” can be used\. The “\*” provides matching against an S3 path and matches everything after the prefix\. For example, the following policy\.

```
S3 resource = "sales-reports/americas/*"
```

This matches the following S3 paths\.

```
sales-reports/americas/year=2020/
sales-reports/americas/year=2019/
sales-reports/americas/year=2019/month=12/day=1/afile.parquet 
sales-reports/americas/year=2018/month=6/day=1/afile.parquet 
sales-reports/americas/year=2017/afile.parquet
```

The “?” wildcard matches only a single character\. For example, for the policy\.

```
S3 resource = "sales-reports/americas/year=201?/"
```

This matches the following S3 paths\.

```
sales-reports/americas/year=2019/
sales-reports/americas/year=2018/
sales-reports/americas/year=2017/
```

#### Wildcards in Users<a name="emr-ranger-emrfs-considerations-wildcards-in-users"></a>

There are two built\-in wildcards when assigning users to provide access to users\. The first is the “\{USER\}” wildcard that provides access to all users\. The second wildcard is “\{OWNER\}”, which provides access to the owner of a particular object or directly\. However, the “\{USER\}” wildcard is currently not supported\.

#### Security Zones<a name="emr-ranger-emrfs-considerations-security-zones"></a>

When using security zones with the S3 Ranger plugin, its advisable to provide both the bucket and prefix resources\. For example:

```
- sthreeResource = "sales-reports"
- sthreeResource = "sales-reports/*"
```

If only the bucket or the prefix is specified, then the Apache Ranger plugin may not recognize the provided S3 resource as part of the security zone\.

#### Other Considerations and Limitations<a name="emr-ranger-emrfs-considerations-limitations"></a>

Below are limitations of the EMRFS S3 Plugin\.
+ Apache Ranger policies can have at most three policies\.
+ Access to S3 must be done through EMRFS and can be used with Hadoop related applications\. The following is not supported:

  \- Boto3 libraries

  \- AWS SDK and AWK CLI

  \- S3A open source connector
+ Apache Ranger deny policies are not supported\.
+ Operations on S3 with keys having CSE\-KMS encryption are currently not supported with the S3 Ranger Plugin\.
+ Cross\-Region support is not supported\.
+ The Hadoop user does not generate any audit events as Hadoop always accesses the EC2 Instance Profile\.
+ It's recommended that you disable EMR Consistency View\. S3 is strongly consistent, so it is no longer needed\. See [Amazon S3 Strong Consistency](https://aws.amazon.com/s3/consistency/) for more information\.
+ The EMRFS S3 Plugin makes numerous STS calls\. It's advised that you do load testing on a development account and monitor STS call volume\. It is also recommended that you make an STS request to raise AssumeRole service limits\.