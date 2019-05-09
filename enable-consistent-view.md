# Enable Consistent View<a name="enable-consistent-view"></a>

You can enable Amazon S3 server\-side encryption or consistent view for EMRFS using the AWS Management Console, AWS CLI, or the `emrfs-site` configuration classification\.<a name="enable-emr-fs-console"></a>

**To configure consistent view using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose settings for **Step 1: Software and Steps** and **Step 2: Hardware**\. 

1. For **Step 3: General Cluster Settings**, under **Additional Options**, choose **EMRFS consistent view**\.

1. For **EMRFS Metadata store**, type the name of your metadata store\. The default value is **EmrFSMetadata**\. If the EmrFSMetadata table does not exist, it is created for you in DynamoDB\.
**Note**  
Amazon EMR does not automatically remove the EMRFS metadata from DynamoDB when the cluster is terminated\.

1. For **Number of retries**, type an integer value\. If an inconsistency is detected, EMRFS tries to call Amazon S3 this number of times\. The default value is **5**\. 

1. For **Retry period \(in seconds\)**, type an integer value\. This is the amount of time that EMRFS waits between retry attempts\. The default value is **10**\.
**Note**  
Subsequent retries use an exponential backoff\. 

**To launch a cluster with consistent view enabled using the AWS CLI**

We recommend that you install the current version of AWS CLI\. To download the latest release, see [https://aws\.amazon\.com//cli/](https://aws.amazon.com/cli/)\.
+ 
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  1. aws emr create-cluster --instance-type m4.large --instance-count 3 --emrfs Consistent=true \
  2. --release-label emr-5.23.0 --ec2-attributes KeyName=myKey
  ```

**To check if consistent view is enabled using the AWS Management Console**
+ To check whether consistent view is enabled in the console, navigate to the **Cluster List** and select your cluster name to view **Cluster Details**\. The "EMRFS consistent view" field has a value of `Enabled` or `Disabled`\.

**To check if consistent view is enabled by examining the `emrfs-site.xml` file**
+ You can check if consistency is enabled by inspecting the `emrfs-site.xml` configuration file on the master node of the cluster\. If the Boolean value for `fs.s3.consistent` is set to `true` then consistent view is enabled for file system operations involving Amazon S3\.