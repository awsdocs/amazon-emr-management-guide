# Enhanced Step Debugging<a name="emr-enhanced-step-debugging"></a>

If an Amazon EMR step fails and you submitted your work using the Step API operation with an AMI of version 5\.x or later, Amazon EMR can identify and return the root cause of the step failure in some cases, along with the name of the relevant log file and a portion of the application stack trace via API\. For example, the following failures can be identified: 
+ A common Hadoop error such as the output directory already exists, the input directory does not exist, or an application runs out of memory\.
+ Java errors such as an application that was compiled with an incompatible version of Java or run with a main class that is not found\.
+ An issue accessing objects stored in Amazon S3\.

This information is available using the [DescribeStep](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_DescribeStep.html) and [ListSteps](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_ListSteps.html) API operations\. The [FailureDetails](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_FailureDetails.html) field of the [StepSummary](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_StepSummary.html) returned by those operations\. To access the FailureDetails information, use the AWS CLI, console, or AWS SDK\.

**To view failure details using the AWS Console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Cluster List** and select a cluster\.

1. Select the arrow icon next to each step to view more details\.

If the step has failed and Amazon EMR can identify the root cause, you see the details of the failure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/DebuggingFailedStep.png)

**To view failure details using the AWS CLI**
+ To get failure details for a step using the AWS CLI, use the describe\-step command\.

  ```
  aws emr describe-step –cluster-id j-1K48XXXXXHCB –step-id s-3QM0XXXXXM1W
  ```

  The output will look similar to the following:

  ```
  {
    "Step": {
      "Status": {
        "FailureDetails": {
          "LogFile": "s3://myBucket/logs/j-1K48XXXXXHCB/steps/s-3QM0XXXXXM1W/stderr.gz",
          "Message": "org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory s3://myBucket/logs/beta already exists",
          "Reason": "Output directory already exists."
        },
        "Timeline": {
          "EndDateTime": 1469034209.143,
          "CreationDateTime": 1469033847.105,
          "StartDateTime": 1469034202.881
        },
        "State": "FAILED",
        "StateChangeReason": {}
      },
      "Config": {
        "Args": [
          "wordcount",
          "s3://myBucket/input/input.txt",
          "s3://myBucket/logs/beta"
        ],
        "Jar": "s3://myBucket/jars/hadoop-mapreduce-examples-2.7.2-amzn-1.jar",
        "Properties": {}
      },
      "Id": "s-3QM0XXXXXM1W",
      "ActionOnFailure": "CONTINUE",
      "Name": "ExampleJob"
    }
  }
  ```