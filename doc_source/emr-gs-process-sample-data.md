# Step 4: Process Your Sample Data By Running a Hive Script<a name="emr-gs-process-sample-data"></a>

 In this step of the tutorial, you run your Hive script in your cluster as a step in the Amazon EMR console to process your sample data\. In Amazon EMR, a *step* is a unit of work that contains one or more Hadoop jobs\. You can submit steps when you create the cluster or when the cluster is running \(if it is a long\-running cluster\)\. 

**Topics**
+ [Submit the Hive Script as a Step](#emr-gs-add-step)
+ [View the Results](#w3ab1c16c19b9)

## Submit the Hive Script as a Step<a name="emr-gs-add-step"></a>

Use the **Add Step** option to submit your Hive script to the cluster using the console\. The Hive script and sample data used by the script have been uploaded to Amazon S3 for you\. 

**Note**  
 Before you run the script, you must have an Amazon S3 bucket and `output ` folder as described in [Create an Amazon S3 Bucket](emr-gs-prerequisites.md#emr-gs-create-bucket)\. 

**To submit your Hive script as a step**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. In **Cluster List**, select the name of your cluster\.

1. Scroll to the **Steps** section and expand it, then choose **Add step**\.

1. In the **Add Step** dialog:
   + For **Step type**, choose **Hive program**\.
   + For **Name**, accept the default name \(Hive program\) or type a new name\.
   + For **Script S3 location**, type **s3://*region*\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q** 

     Replace *region* with your region\. For example, for US West \(Oregon\) type **s3://us\-west\-2\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q**
   + For **Input S3 location**, type **s3://*region*\.elasticmapreduce\.samples** 

     Replace *region* with your region\. For example, for US West \(Oregon\) type **s3://us\-west\-2\.elasticmapreduce\.samples**
   + For **Output S3 location**, type or browse to the `output` bucket that you created in [Create an Amazon S3 Bucket](emr-gs-prerequisites.md#emr-gs-create-bucket)\.
   + For **Arguments**, include the following argument to allow column names that are the same as reserved words:

     ```
     -hiveconf hive.support.sql11.reserved.keywords=false
     ```
   + For **Action on failure**, accept the default option **Continue**\.

1. Choose **Add**\. The step appears in the console with a status of **Pending**\. 

1. The status of the step changes from **Pending** to **Running** to **Completed** as the step runs\. To update the status, choose **Refresh** above the **Actions** column\. The step runs in approximately 1 minute\.

## View the Results<a name="w3ab1c16c19b9"></a>

After the step completes successfully, the query output produced by the Hive script is stored in the Amazon S3 output folder that you specified when you submitted the step\. 

**To view the output of the Hive script**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the Amazon S3 console, select the bucket that you used for the output data; for example, `s3://myemrbucket/`

1. Select the `output` folder\. 

1. The query writes results into a separate folder\. Choose `os_requests`\. 

1. The Hive query results are stored in a text file\. To download the file, right\-click it, choose **Download**, open the context \(right\-click\) menu for **Download**, choose **Save Link As**, and then save the file to a suitable location\. 

1. Open the file using a text editor such as WordPad \(Windows\), TextEdit \(Mac OS\), or gEdit \(Linux\)\. In the output file, you should see the number of access requests by operating system\. 

1. Proceed to the next step\. 