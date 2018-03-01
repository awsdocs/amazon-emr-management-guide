# Step 5: Reset Your Environment<a name="emr-gs-reset-environment"></a>

After you have completed this tutorial, you should remove your Amazon S3 bucket and terminate your Amazon EMR cluster to avoid incurring additional charges\.

**Deleting Your Amazon S3 Bucket**  
 You cannot delete an Amazon S3 bucket that has items in it\. First, delete your `logs` and `output` folders, and then delete your bucket\. For more information about deleting folders and buckets, go to [Delete an Object and Bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/DeletingAnObjectandBucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. 

**Terminating Your Sample Cluster**  
Terminating your cluster terminates the associated Amazon EC2 instances and stops the accrual of Amazon EMR charges\. Amazon EMR preserves metadata information about completed clusters for your reference, at no charge, for two months\. The console does not provide a way to delete completed clusters from the console; these are automatically removed for you after two months\. 

**To terminate your Amazon EMR cluster**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Cluster List** page, select your cluster and choose **Terminate**\. 

1. Clusters are often created with termination protection on, which helps prevent accidental shutdown\. If termination protection is on, you are prompted to change the setting\. Choose **Change**, **Off**\.

1. Choose **Terminate**\. 