# Supported Applications and Features<a name="emr-lf-scope"></a>

## Supported Applications<a name="emr-lf-apps"></a>

The integration between Amazon EMR and AWS Lake Formation supports the following applications: 
+ Apache Spark
+ Apache Zeppelin
+ Amazon EMR notebooks

**Important**  
Other applications are currently not supported\. To ensure the security of your cluster, do not install applications other than those in this list\.

## Supported Features<a name="emr-lf-features"></a>

The following Amazon EMR features can be used with EMR and Lake Formation: 
+ Encryption at rest and in transit 
+ Kerberos authentication using a cluster\-dedicated KDC 
+ Instance groups, instance fleets, and spot instances 
+ Reconfiguring applications on a running cluster

The following EMR features currently do not work with Lake Formation integration: 
+ Steps
+ Multiple master nodes 
+ EMRFS consistent view
+ EMRFS CSE\-C and SSE\-C using customer\-provided encryption keys

## Limitations<a name="emr-lf-limitations"></a>

Consider the following limitations when using Amazon EMR with AWS Lake Formation:
+ In Lake Formation enabled clusters, Spark SQL can only read from data managed by AWS Glue Data Catalog and cannot access data managed outside of AWS Glue or Lake Formation\. Data from other sources can be accessed using non\-Spark SQL operations if the IAM role for other AWS Services chosen during cluster deployment has policies in place allowing the cluster to access those data sources\.
  + For example, you might have two Amazon S3 buckets and an Amazon DynamoDB table that you want your Spark job to access in addition to a set of Lake Formation tables\. In this case, you could create a role that can access the two Amazon S3  buckets, and the Amazon DynamoDB table and use it for the `IAM role for other AWS services` when launching your cluster\.
+ Spark job submission must be done through EMR notebooks, Zeppelin, or Livy\. Spark jobs submitted through `spark-submit` will not work with Lake Formation at this time\.
+ Spark SQL can only read from Lake Formation tables\. Using Spark SQL to write to tables or create new tables in Lake Formation is not currently supported\.
+ Using Spark SQL to access Lake Formation tables that use the Hive Map data type is currently not supported\.
+ When using Spark to access data stored in a columnar format, performance optimizations such as predicate pushdown and vectorized reads are not supported\. Those Spark SQL applications that rely on these optimizations will see reduced performance when used with Lake Formation\.
+ There is currently no central logout available for Amazon EMR notebooks and Zeppelin\.
+ When using Spark SQL to access data protected by Lake Formation, AWS CloudTrail entries for data access only contain the name of the IAM role associated with the Amazon EMR cluster\. They do not contain the federated user using the notebook\. 
+ Spark's fallback to HDFS for statistics collection capability is not supported in this release with Lake Formation\. The property `spark.sql.statistics.fallBackToHdfs ` for this feature is disabled by default\. This feature does not work when this property is manually enabled\.
+ Querying tables that contain partitions under different table paths in Amazon S3 is currently not supported\.
+ It is important to understand that the Lake Formation column level authorization prevents users from accessing data in columns the user does not have access to\. However, in certain situations, users are able to access metadata describing all columns in the table, including the columns the user does not have access to\. This column metadata is stored in the table’s table properties for tables that use the Avro storage format or that use custom Serializer/Deserializers \(SerDe\) in which the table schema is defined in table properties along with the SerDe definition\. When you use Amazon EMR and Lake Formation, we recommend that you review the contents of the table properties for the tables you’re protecting and, where possible, limit the information stored in table properties to prevent any sensitive metadata from being visible to users\.