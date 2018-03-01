# Logging Amazon EMR API Calls in AWS CloudTrail<a name="logging_emr_api_calls"></a>

Amazon EMR is integrated with AWS CloudTrail, a service that captures API calls made by or on behalf of your AWS account\. This information is collected and written to log files that are stored in an Amazon S3 bucket that you specify\. API calls are logged when you use the Amazon EMR API, the Amazon EMR console, a back\-end console, or the AWS CLI\. Using the information collected by CloudTrail, you can determine what request was made to Amazon EMR, the source IP address the request was made from, who made the request, when it was made, and so on\. 

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/) 


+ [Amazon EMR Information in CloudTrail](#emr_info_in_ct)
+ [Understanding Amazon EMR Log File Entries](#understanding_emr_log_file_entries)

## Amazon EMR Information in CloudTrail<a name="emr_info_in_ct"></a>

If CloudTrail logging is turned on, calls made to all Amazon EMR actions are captured in log files\. All of the Amazon EMR actions are documented in the [Amazon EMR API Reference](http://docs.aws.amazon.com/ElasticMapReduce/latest/API/)\. For example, calls to the **ListClusters**, **DescribeCluster**, and **RunJobFlow** actions generate entries in CloudTrail log files\. 

Every log entry contains information about who generated the request\. For example, if a request is made to create and run a new job flow \(**RunJobFlow**\), CloudTrail logs the user identity of the person or service that made the request\. The user identity information helps you determine whether the request was made with root or IAM user credentials, with temporary security credentials for a role or federated user, or by another AWS service\. For more information about CloudTrail fields, see [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/eventreference.html) in the *AWS CloudTrail User Guide*\. 

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\. 

## Understanding Amazon EMR Log File Entries<a name="understanding_emr_log_file_entries"></a>

CloudTrail log files can contain one or more log entries composed of multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, any input parameters, the date and time of the action, and so on\. The log entries do not appear in any particular order\. That is, they do not represent an ordered stack trace of the public API calls\. 

The following log file record shows that an IAM user called the **RunJobFlow** action by using the SDK\. 

```
{
	"Records": [
	{
         "eventVersion":"1.01",
         "userIdentity":{
            "type":"IAMUser",
            "principalId":"EX_PRINCIPAL_ID",
            "arn":"arn:aws:iam::123456789012:user/temporary-user-xx-7M",
            "accountId":"123456789012",
            "accessKeyId":"EXAMPLE_KEY_ID",
            "userName":"temporary-user-xx-7M"
         },
         "eventTime":"2014-03-31T17:59:21Z",
         "eventSource":"elasticmapreduce.amazonaws.com",
         "eventName":"RunJobFlow",
         "awsRegion":"us-east-1",
         "sourceIPAddress":"127.0.0.1",
         "userAgent":"aws-sdk-java/unknown-version Linux/xx Java_HotSpot(TM)_64-Bit_Server_VM/xx",
         "requestParameters":{
            "tags":[
               {
                  "value":"prod",
                  "key":"domain"
               },
               {
                  "value":"us-east-1",
                  "key":"realm"
               },
               {
                  "value":"VERIFICATION",
                  "key":"executionType"
               }
            ],
            "instances":{
               "slaveInstanceType":"m3.xlarge",
               "ec2KeyName":"emr-integtest",
               "instanceCount":1,
               "masterInstanceType":"m3.xlarge",
               "keepJobFlowAliveWhenNoSteps":true,
               "terminationProtected":false
            },
            "visibleToAllUsers":false,
            "name":"Integ 3xm1large",
            "amiVersion":"3.0.4"
         },
         "responseElements":{
            "jobFlowId":"j-2WDJCGEG4E6AJ"
         },
         "requestID":"2f482daf-b8fe-11e3-89e7-75a3d0e071c5",
         "eventID":"b348a38d-f744-4097-8b2a-e68c9b424698"
      },
	...additional entries
  ]
}
```