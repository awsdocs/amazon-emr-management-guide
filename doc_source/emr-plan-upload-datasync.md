# Upload data with AWS DataSync<a name="emr-plan-upload-datasync"></a>

AWS DataSync is an online data transfer service that simplifies, automates, and accelerates the process of moving data between your on\-premises storage and AWS storage services or between AWS storage services\. DataSync supports a variety of on\-premises storage systems such as Hadoop Distributed File System \(HDFS\), NAS file servers, and self\-managed object storage\.

The most common way to get data onto a cluster is to upload the data to Amazon S3 and use the built\-in features of Amazon EMR to load the data onto your cluster\.

DataSync can help you accomplish the following tasks:
+ Replicate HDFS on your Hadoop cluster to Amazon S3 for business continuity
+ Copy HDFS to Amazon S3 to populate your data lakes
+ Transfer data between your Hadoop cluster's HDFS and Amazon S3 for analysis and processing

To upload data to your S3 bucket, you first deploy one or more DataSync agents in the same network as your on\-premises storage\. An *agent* is a virtual machine \(VM\) that is used to read data from or write data to a self\-managed location\. You then activate your agents in the AWS account and AWS Region where your S3 bucket is located\.

After your agent is activated, you create a source location for your on\-premises storage, a destination location for your S3 bucket, and a task\. A *task* is a set of two locations \(source and destination\) and a set of default options that you use to control the behavior of the task\.

Finally, you run your DataSync task to transfer data from the source to the destination\. 

For more information, see [Getting started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html)\.