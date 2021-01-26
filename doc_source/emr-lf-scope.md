# Applications, Features, and Limitations<a name="emr-lf-scope"></a>

## Supported Applications<a name="emr-lf-apps"></a>

The integration between Amazon EMR and AWS Lake Formation supports the following applications: 
+ Amazon EMR notebooks
+ Apache Zeppelin
+ Apache Spark through Amazon EMR notebooks

**Important**  
Other applications are currently not supported\. To ensure the security of your cluster, do not install applications that do not appear in this list\.

## Supported Features<a name="emr-lf-features"></a>

The following Amazon EMR features can be used with EMR and Lake Formation: 
+ Encryption at rest and in transit 
+ Kerberos authentication using a cluster\-dedicated key distribution center \(KDC\) without cross\-realm trust
+ Instance groups, instance fleets, and Spot Instances 
+ Reconfiguring applications on a running cluster
+ EMRFS server\-side encryption \(SSE\)
**Note**  
Amazon EMR encryption settings govern SSE\. For more information, see [Encryption Options](emr-data-encryption-options.md)\.

The following EMR features currently do not work with Lake Formation integration: 
+ Steps
+ Multiple master nodes 
+ EMRFS consistent view
+ EMRFS client\-side encryption \(CSE\)

## Limitations<a name="emr-lf-limitations"></a>

Consider the following limitations when using Amazon EMR with AWS Lake Formation:
+ Amazon EMR with Lake Formation is currently available in 16 AWS Regions: US East \(Ohio and N\. Virginia\), US West \(N\. California and Oregon\), Asia Pacific \(Mumbai, Seoul, Singapore, Sydney, and Tokyo\), Canada \(Central\), Europe \(Frankfurt, Ireland, London, Paris, and Stockholm\), South America \(São Paulo\)\. 
+ Amazon EMR with Lake Formation does not currently work with AWS Single Sign\-On \(SSO\)\.
+ You must use a user\-defined IAM role instead of the [Lake Formation service\-linked role](https://docs.aws.amazon.com/lake-formation/latest/dg/service-linked-roles.html) to register data locations used by Amazon EMR clusters with Lake Formation\. Lake Formation does not support using its service\-linked role when you integrate with EMR\. For information about creating a user\-defined role to register data locations with Lake Formation, see [Requirements for Roles Used to Register Locations](https://docs.aws.amazon.com/lake-formation/latest/dg/registration-role.html)\.
+ It is important to understand that the Lake Formation column\-level authorization prevents users from accessing data in columns that the user does not have access to\. However, in certain situations, users are able to access metadata describing all columns in the table, including the columns the user does not have access to\. This column metadata is stored in the table’s table properties for tables that use the Avro storage format or that use custom Serializer/Deserializers \(SerDe\) in which the table schema is defined in table properties along with the SerDe definition\. When you use Amazon EMR and Lake Formation, we recommend that you review the contents of the table properties for the tables you’re protecting and, where possible, limit the information stored in table properties to prevent any sensitive metadata from being visible to users\.
+ In Lake Formation enabled clusters, Spark SQL can only read from data managed by AWS Glue Data Catalog and cannot access data managed outside of AWS Glue or Lake Formation\. Data from other sources can be accessed using non\-Spark SQL operations if the IAM role for other AWS Services chosen during cluster deployment has policies in place allowing the cluster to access those data sources\.
  + For example, you might have two Amazon S3 buckets and an Amazon DynamoDB table that you want your Spark job to access in addition to a set of Lake Formation tables\. In this case, you could create a role that can access the two Amazon S3 buckets, and the Amazon DynamoDB table and use it for the `IAM role for other AWS services` when launching your cluster\.
+ Spark job submission must be done through EMR notebooks, Zeppelin, or Livy\. Spark jobs submitted through `spark-submit` will not work with Lake Formation at this time\.
+ Spark SQL can only read from Lake Formation tables\. Using Spark SQL to write to tables or create new tables in Lake Formation is not currently supported\.
+ Using Spark SQL to access Lake Formation tables that use the Hive Map data type is currently not supported\.
+ There is currently no central logout available for Amazon EMR notebooks and Zeppelin\.
+ When using Spark SQL to access data protected by Lake Formation, AWS CloudTrail entries for data access only contain the name of the IAM role associated with the Amazon EMR cluster\. They do not contain the federated user using the notebook\. 
+ Spark's fallback to HDFS for statistics collection capability is not supported in this release with Lake Formation\. The property `spark.sql.statistics.fallBackToHdfs ` for this feature is disabled by default\. This feature does not work when this property is manually enabled\.
+ Querying tables that contain partitions under different table paths in Amazon S3 is currently not supported\.
+ Cross\-realm trust for Kerberos authentication is currently not supported\.