# Authorizing Access to EMRFS Data in Amazon S3<a name="emr-plan-credentialsprovider"></a>

By default, the role that you specify as the EC2 instance profile for Amazon EMR determines the permissions for accessing EMRFS data in Amazon S3\. The IAM policies that are attached to this role apply regardless of the user or group making the request through EMRFS\. The default is `EMR_EC2_DefaultRole`\.

Beginning with Amazon EMR release version 5\.10\.0, you can use a security configuration to set up EMRFS authorization for a cluster, which allows you to implement fine\-grained access control to EMRFS data in Amazon S3 for clusters that have multiple users\. You can specify different IAM roles for different users and groups\. The EC2 instance profile can assume a role based on the user or group that makes the request\. In addition, you can specify different IAM roles based on the location of EMRFS data in Amazon S3\. For more information, see [Configure EMRFS Authorization for Data in Amazon S3](emr-emrfs-authz.md)\.

Alternatively, you can define a custom credentials provider class, which allows you to customize access to EMRFS data in Amazon S3 if your Amazon EMR solution prevents you from using EMRFS authorization\.

## Creating a Custom Credentials Provider for EMRFS Data in Amazon S3<a name="emr-create-credentialsprovider"></a>

To create a custom credentials provider, you implement the [AWSCredentialsProvider](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/AWSCredentialsProvider.html) and the Hadoop [Configurable](https://hadoop.apache.org/docs/stable/api/org/apache/hadoop/conf/Configurable.html) classes\.

For a detailed explanation of this approach, see [Securely Analyze Data from Another AWS Account with EMRFS](http://aws.amazon.com/blogs/big-data/securely-analyze-data-from-another-aws-account-with-emrfs) in the AWS Big Data blog\. The blog post includes a tutorial that walks you through the process end\-to\-end, from creating IAM roles to launching the cluster\. It also provides a Java code example that implements the custom credential provider class\.

The basic steps are as follows:

**To specify a custom credentials provider**

1. Create a custom credentials provider class compiled as a JAR file\.

1. Run a script as a bootstrap action to copy the custom credentials provider JAR file to the `/usr/share/aws/emr/emrfs/auxlib` location on the cluster's master node\. For more information about bootstrap actions, see [\(Optional\) Create Bootstrap Actions to Install Additional Software](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-bootstrap.html)\.

1. Customize the `emrfs-site` classification to specify the class that you implement in the JAR file\. For more information about specifying configuration objects to customize applications, see [Configuring Applications](http://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-configure-apps.html) in the *Amazon EMR Release Guide*\.

   The following example demonstrates a `create-cluster` command that launches a Hive cluster with common configuration parameters, and also includes:

   + A bootstrap action that runs the script, `copy_jar_file.sh`, which is saved to `mybucket` in Amazon S3\.

   + An `emrfs-site` classification that specifies a custom credentials provider defined in the JAR file as `MyCustomCredentialsProvider`
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   ```
   aws emr create-cluster --applications Name=Hive \
   --bootstrap-actions '[{"Path":"s3://mybucket/copy_jar_file.sh","Name":"Custom action"}]' \
   --ec2-attributes '{"KeyName":"MyKeyPair","InstanceProfile":"EMR_EC2_DefaultRole",\
   "SubnetId":"subnet-xxxxxxxx","EmrManagedSlaveSecurityGroup":"sg-xxxxxxxx",\
   "EmrManagedMasterSecurityGroup":"sg-xxxxxxxx"}' \
   --service-role EMR_DefaultRole --enable-debugging --release-label emr-5.12.0 \
   --log-uri 's3n://my-emr-log-bucket/' --name 'test-awscredentialsprovider-emrfs' \
   --instance-type=m3.xlarge --instance-count 3  \
   --configurations '[{"Classification":"emrfs-site",\
   "Properties":{"fs.s3.customAWSCredentialsProvider":"MyAWSCredentialsProviderWithUri"},\
   "Configurations":[]}]'
   ```