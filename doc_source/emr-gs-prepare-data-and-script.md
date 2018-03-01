# Step 3: Prepare Your Sample Data and Script<a name="emr-gs-prepare-data-and-script"></a>

 For this tutorial, the sample data and sample script are already provided for you\. However, this step describes them so that you understand how they fit into the overall process of using Amazon EMR to analyze data\.


+ [Sample Data Overview](#emr-gs-sample-data-overview)
+ [Sample Hive Script Overview](#emr-gs-hive-script-overview)

## Sample Data Overview<a name="emr-gs-sample-data-overview"></a>

The sample data is a series of Amazon CloudFront web distribution log files\. The data is stored in Amazon S3 at **s3://*region*\.elasticmapreduce\.samples** where *region* is your region\.

Each entry in the CloudFront log files provides details about a single user request in the following format:

```
2014-07-05 20:00:00 LHR3 4260 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-1.jpeg 200 - Mozilla/5.0%20(MacOS;%20U;%20Windows%20NT%205.1;%20en-US;%20rv:1.9.0.9)%20Gecko/2009040821%20IE/3.0.9
```

## Sample Hive Script Overview<a name="emr-gs-hive-script-overview"></a>

The sample script calculates the total number of requests per operating system over a specified timeframe\. The script uses HiveQL, which is a SQL\-like scripting language for data warehousing and analysis\. The script is stored in Amazon S3 at **s3://*region*\.elasticmapreduce\.samples/cloudfront/code/Hive\_CloudFront\.q** where *region* is your region\.

The sample Hive script does the following: 

+ Creates a Hive table named `cloudfront_logs`\.

+ Reads the CloudFront log files from Amazon S3 using EMRFS and parses the CloudFront log files using the regular expression serializer/deserializer \(RegEx SerDe\)\.

+ Writes the parsed results to the Hive table `cloudfront_logs`\.

+ Submits a HiveQL query against the data to retrieve the total requests per operating system for a given time frame\.

+ Writes the query results to your Amazon S3 output bucket\.

 The Hive code that creates the table looks like the following:

```
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
```

 The Hive code that parses the log files using the RegEx SerDe looks like the following: 

```
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe' 
WITH SERDEPROPERTIES ( 
  "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
) LOCATION '${INPUT}/cloudfront/data/';
```

The HiveQL query looks like the following:

```
INSERT OVERWRITE DIRECTORY '${OUTPUT}/os_requests/' SELECT os, COUNT(*) count FROM cloudfront_logs WHERE dateobject BETWEEN '2014-07-05' AND '2014-08-05' GROUP BY os;
```