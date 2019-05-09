# Step 4: Process Data By Running The Hive Script as a Step<a name="emr-gs-process-sample-data"></a>

With your cluster up and running, you can now submit a Hive script\. In this tutorial, you submit the Hive script as a step using the Amazon EMR console\. In Amazon EMR, a *step* is a unit of work that contains one or more jobs\. As you learned in [Step 2: Launch Your Sample Amazon EMR Cluster](emr-gs-launch-sample-cluster.md), you can submit steps to a long\-running cluster, which is what we do in this step\. You can also specify steps when you create a cluster, or you could connect to the master node, create the script in the local file system, and run it using the command line, for example `hive -f Hive_CloudFront.q`\.

## Understanding The Data And Script<a name="emr-gs-data-and-script"></a>

The sample data and script that you use in this tutorial are already available in an Amazon S3 location that you can access\.

The sample data is a series of Amazon CloudFront access log files\. For more information about CloudFront and log file formats, see [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/)\. The data is stored in Amazon S3 at **s3://*region*\.elasticmapreduce\.samples/cloudfront/data** where *region* is your region, for example, *us\-west\-2*\. When you enter the location when you submit the step, you omit the `cloudfront/data` portion because the script adds it\.

Each entry in the CloudFront log files provides details about a single user request in the following format:

```
2014-07-05 20:00:00 LHR3 4260 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-1.jpeg 200 - Mozilla/5.0%20(MacOS;%20U;%20Windows%20NT%205.1;%20en-US;%20rv:1.9.0.9)%20Gecko/2009040821%20IE/3.0.9
```

The sample script calculates the total number of requests per operating system over a specified time frame\. The script uses HiveQL, which is a SQL\-like scripting language for data warehousing and analysis\. The script is stored in Amazon S3 at **s3://*region*\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q** where *region* is your region\. 

The sample Hive script does the following: 
+ Creates a Hive table schema named `cloudfront_logs`\. For more information about Hive tables, see the [Hive Tutorial](https://cwiki.apache.org/confluence/display/Hive/Tutorial) on the Hive wiki\.
+ Uses the built\-in regular expression serializer/deserializer \(RegEx SerDe\) to parse the input data and apply the table schema\. For more information, see [SerDe](https://cwiki.apache.org/confluence/display/Hive/SerDe) on the Hive wiki\.
+ Runs a HiveQL query against the `cloudfront_logs` table and writes the query results to the Amazon S3 output location that you specify\.

The contents of the `Hive_CloudFront.q` script are shown below\. The `${INPUT}` and `${OUTPUT}` variables are replaced by the Amazon S3 locations that you specify when you submit the script as a step\. When you reference data in Amazon S3 as this script does, Amazon EMR uses the EMR File System \(EMRFS\) to read input data and write output data\.

```
-- Summary: This sample shows you how to analyze CloudFront logs stored in S3 using Hive

-- Create table using sample data in S3.  Note: you can replace this S3 path with your own.
CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
  DateObject Date,
  Time STRING,
  Location STRING,
  Bytes INT,
  RequestIP STRING,
  Method STRING,
  Host STRING,
  Uri STRING,
  Status INT,
  Referrer STRING,
  OS String,
  Browser String, 
  BrowserVersion String
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
  "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
) LOCATION '${INPUT}/cloudfront/data';

-- Total requests per operating system for a given time frame
INSERT OVERWRITE DIRECTORY '${OUTPUT}/os_requests/' SELECT os, COUNT(*) count FROM cloudfront_logs WHERE dateobject BETWEEN '2014-07-05' AND '2014-08-05' GROUP BY os;
```

## Submit the Hive Script as a Step<a name="emr-gs-add-step"></a>

Use the **Add Step** option to submit your Hive script to the cluster using the console\. The Hive script and sample data have been uploaded to Amazon S3, and you specify the output location as the folder you created earlier in [Create an Amazon S3 Bucket](emr-gs-prerequisites.md#emr-gs-create-bucket)\.

**To run the Hive script by submitting it as a step**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. In **Cluster List**, select the name of your cluster\. Make sure the cluster is in a **Waiting** state\.

1. Choose **Steps**, and then choose **Add step**\.

1. Configure the step according to the following guidelines:
   + For **Step type**, choose **Hive program**\.
   + For **Name**, you can leave the default or type a new name\. If you have many steps in a cluster, the name helps you keep track of them\.
   + For **Script S3 location**, type **s3://*region*\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q**\. Replace *region* with your region identifier\. For example, **s3://us\-west\-2\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q** if you are working in the **Oregon** region\. For a list of regions and corresponding Region identifiers, see [AWS Regions and Endpoints for Amazon EMR](https://docs.aws.amazon.com/general/latest/gr/rande.html#emr_region) in the *AWS General Reference*\.
   + For **Input S3 location**, type **s3://*region*\.elasticmapreduce\.samples** 

     Replace *region* with your region identifier\.
   + For **Output S3 location**, type or browse to the `output` bucket that you created in [Create an Amazon S3 Bucket](emr-gs-prerequisites.md#emr-gs-create-bucket)\.
   + For **Action on failure**, accept the default option **Continue**\. This specifies that if the step fails, the cluster continues to run and processes subsequent steps\. The **Cancel and wait** option specifies that a failed step should be canceled, that subsequent steps should not run, abut that the cluster should continue running\. The **Terminate cluster** option specifies that the cluster should terminate if the step fails\.

1. Choose **Add**\. The step appears in the console with a status of **Pending**\.

1. The status of the step changes from **Pending** to **Running** to **Completed** as the step runs\. To update the status, choose the refresh icon to the right of the **Filter**\. The script takes approximately a minute to run\.

## View the Results<a name="emr-gs-view-results"></a>

After the step completes successfully, the Hive query output is saved as a text file in the Amazon S3 output folder that you specified when you submitted the step\.

**To view the output of the Hive script**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **Bucket name** and then the folder that you set up earlier\. For example, *mybucket* and then *MyHiveQueryResults*\.

1. The query writes results to a folder within your output folder named *os\_requests*\. Choose that folder\. There should be a single file named *000000\_0* in the folder\. This is a text file that contains your Hive query results\.

1. Choose the file, and then choose **Download** to save it locally\.

1. Use the text editor that you prefer to open the file\. The output file shows the number of access requests ordered by operating system\. The following example shows the output in WordPad:  
![\[Sample Hive query results in WordPad.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/query-result-get-started.png)