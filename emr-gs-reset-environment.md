# Step 5: Terminate the Cluster and Delete the Bucket<a name="emr-gs-reset-environment"></a>

After you complete the tutorial, you may want to terminate your cluster and delete your Amazon S3 bucket to avoid additional charges\. 

Terminating your cluster terminates the associated Amazon EC2 instances and stops the accrual of Amazon EMR charges\. Amazon EMR preserves metadata information about completed clusters for your reference, at no charge, for two months\. The console does not provide a way to delete terminated clusters so that they aren't viewable in the console\. Terminated clusters are removed from the cluster when the metadata is removed\.

**To terminate the cluster**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**, choose your cluster, and then choose **Terminate**\. 

   Clusters are often created with termination protection on, which helps prevent accidental shutdown\. If you followed the tutorial precisely, termination protection should be off\. If termination protection is on, you are prompted to change the setting as a precaution before terminating the cluster\. Choose **Change**, **Off**\.

**To delete the output bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the bucket from the list, so that the whole bucket row is selected\.

1. Choose delete bucket, type the name of the bucket, and then click **Confirm**\.

   For more information about deleting folders and buckets, go to [How Do I Delete an S3 Bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-bucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. 