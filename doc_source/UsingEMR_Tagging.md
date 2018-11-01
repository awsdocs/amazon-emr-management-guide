# View Cluster Instances in Amazon EC2<a name="UsingEMR_Tagging"></a>

 To help you manage your resources, Amazon EC2 allows you to assign metadata to resources in the form of tags\. Each Amazon EC2 tag consists of a key and a value\. Tags allow you to categorize your Amazon EC2 resources in different ways: for example, by purpose, owner, or environment\. 

 You can search and filter resources based on the tags\. The tags assigned using your AWS account are available only to you\. Other accounts sharing the resource cannot view your tags\. 

Amazon EMR automatically tags each EC2 instance it launches with key\-value pairs that identify the cluster and the instance group to which the instance belongs\. This makes it easy to filter your EC2 instances to show, for example, only those instances belonging to a particular cluster or to show all of the currently running instances in the task\-instance group\. This is especially useful if you are running several clusters concurrently or managing large numbers of EC2 instances\.

These are the predefined key\-value pairs that Amazon EMR assigns:


| Key | Value | 
| --- | --- | 
| aws:elasticmapreduce:job\-flow\-id |  <job\-flow\-identifier>  | 
| aws:elasticmapreduce:instance\-group\-role |  <group\-role>  | 

The values are further defined as follows:
+ The <job\-flow\-identifier> is the ID of the cluster the instance is provisioned for\. It appears in the format j\-XXXXXXXXXXXXX\. 
+ The <group\-role> is one of the following values: master, core, or task\. These values correspond to the master instance group, core instance group, and task instance group\.

 You can view and filter on the tags that Amazon EMR adds\. For more information, see [Using Tags](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the *Amazon EC2 User Guide for Linux Instances*\. Because the tags set by Amazon EMR are system tags and cannot be edited or deleted, the sections on displaying and filtering tags are the most relevant\. 

**Note**  
 Amazon EMR adds tags to the EC2 instance when its status is updated to running\. If there's a latency period between the time the EC2 instance is provisioned and the time its status is set to running, the tags set by Amazon EMR do not appear until the instance starts\. If you don't see the tags, wait for a few minutes and refresh the view\. 