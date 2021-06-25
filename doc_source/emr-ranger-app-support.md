# Application support and limitations<a name="emr-ranger-app-support"></a>

## Supported applications<a name="emr-ranger-app-support-list"></a>

The integration between Amazon EMR and Apache Ranger in which EMR installs Ranger plugins currently supports the following applications:
+ Apache Spark \(Available with EMR 5\.32 and later\)
+ Apache Hive \(Available with EMR 5\.32 and later\)
+ S3 Access through EMRFS \(Available with EMR 5\.32 and later\)

The following applications can be installed on an EMR cluster and may need to be configured to meet your security needs:
+ Apache Hadoop \(Available with EMR 5\.32 and later including YARN and HDFS\)
+ Apache Livy \(Available with EMR 5\.32 and later\)
+ Apache Zeppelin \(Available with EMR 5\.32 and later\)
+ Apache Hue \(Available with EMR 5\.32 and later\)
+ Tez \(Available with EMR 5\.32 and later\)
+ Ganglia \(Available with EMR 5\.32 and later\)
+ ZooKeeper \(Available with EMR 5\.32 and later\)
+ MXNet \(Available with EMR 5\.32 and later\)
+ Mahout \(Available with EMR 5\.32 and later\)
+ HCatalog \(Available with EMR 5\.32 and later\)
+ TensorFlow \(Available with EMR 5\.32 and later\)

**Important**  
Applications listed above are the only applications that are currently supported\. To ensure cluster security, you are allowed to create an EMR cluster with only the applications in the above list when Apache Ranger is enabled\.  
Other applications are currently not supported\. To ensure the security of your cluster, attempting to install other applications will cause the rejection your cluster\.

**Supported Features**  
The following Amazon EMR features can be used with Amazon EMR and Apache Ranger:
+ Encryption at rest and in transit
+ Kerberos authentication \(required\)
+ Instance groups, instance fleets, and Spot Instances
+ Reconfiguration of applications on a running cluster
+ EMRFS server\-side encryption \(SSE\)

**Note**  
Amazon EMR encryption settings govern SSE\. For more information, see [Encryption Options](emr-data-encryption-options.md)\.

## Application limitations<a name="emr-ranger-app-support-limitations"></a>

There are several limitations to keep in mind when you integrate Amazon EMR and Apache Ranger:
+ You cannot currently use the console to create a security configuration that specifies the AWS Ranger integration option in the AWS GovCloud \(US\) Region\. Security configuration can be done using the CLI\.
+ Kerberos must be installed on your cluster\.
+ Application UIs \(user interfaces\) such as the YARN Resource Manager UI, HDFS NameNode UI, and Livy UI are not set with authentication by default\.
+ The HDFS default permissions `umask` are configured so that objects created are set to `world wide readable` by default\.
+ See limitations for each application for additional limitations\.

**Note**  
Amazon EMR encryption settings govern SSE\. For more information, see [Encryption Options](emr-data-encryption-options.md)\.