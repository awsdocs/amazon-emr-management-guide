# Authorizing Access to EMRFS Data in Amazon S3<a name="emr-plan-credentialsprovider"></a>

By default, the EMR role for EC2 determines the permissions for accessing EMRFS data in Amazon S3\. The IAM policies that are attached to this role apply regardless of the user or group making the request through EMRFS\. The default is `EMR_EC2_DefaultRole`\. For more information, see [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md)\.

Beginning with Amazon EMR release version 5\.10\.0, you can use a security configuration to specify IAM roles for EMRFS\. This allows you to customize permissions for EMRFS requests to Amazon S3 for clusters that have multiple users\. You can specify different IAM roles for different users and groups, and for different Amazon S3 bucket locations based on the prefix in Amazon S3\. When EMRFS makes a request to Amazon S3 that matches users, groups, or the locations that you specify, the cluster uses the corresponding role that you specify instead of the EMR role for EC2\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.

Alternatively, if your Amazon EMR solution has demands beyond what IAM roles for EMRFS provides, you can define a custom credentials provider class, which allows you to customize access to EMRFS data in Amazon S3\.

## Creating a Custom Credentials Provider for EMRFS Data in Amazon S3<a name="emr-create-credentialsprovider"></a>

To create a custom credentials provider, you implement the [AWSCredentialsProvider](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/AWSCredentialsProvider.html) and the Hadoop [Configurable](https://hadoop.apache.org/docs/stable/api/org/apache/hadoop/conf/Configurable.html) classes\.

For a detailed explanation of this approach, see [Securely Analyze Data from Another AWS Account with EMRFS](http://aws.amazon.com/blogs/big-data/securely-analyze-data-from-another-aws-account-with-emrfs) in the AWS Big Data blog\. The blog post includes a tutorial that walks you through the process end\-to\-end, from creating IAM roles to launching the cluster\. It also provides a Java code example that implements the custom credential provider class\.

The basic steps are as follows:

**To specify a custom credentials provider**

1. Create a custom credentials provider class compiled as a JAR file\.

1. Run a script as a bootstrap action to copy the custom credentials provider JAR file to the `/usr/share/aws/emr/emrfs/auxlib` location on the cluster's master node\. For more information about bootstrap actions, see [\(Optional\) Create Bootstrap Actions to Install Additional Software](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-bootstrap.html)\.

1. Customize the `emrfs-site` classification to specify the class that you implement in the JAR file\. For more information about specifying configuration objects to customize applications, see [Configuring Applications](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-configure-apps.html) in the *Amazon EMR Release Guide*\.

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
   --service-role EMR_DefaultRole --enable-debugging --release-label emr-5.29.0 \
   --log-uri 's3n://my-emr-log-bucket/' --name 'test-awscredentialsprovider-emrfs' \
   --instance-type=m5.xlarge --instance-count 3  \
   --configurations '[{"Classification":"emrfs-site",\
   "Properties":{"fs.s3.customAWSCredentialsProvider":"MyAWSCredentialsProviderWithUri"},\
   "Configurations":[]}]'
   ```