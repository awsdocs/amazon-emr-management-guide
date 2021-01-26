# When to set up EMR events in CloudWatch<a name="emr-service-limits-cloudwatch-events"></a>

For some polling APIs, such as DescribeCluster, DescribeStep, and ListClusters, setting up a ﻿CouldWatch event﻿ can reduce the response time to changes and free up your service quotas\. For example, if you have a Lambda function set up to run when a cluster’s state changes, such as when a step completes or a cluster terminates, you can use that trigger to start the next action in your workflow instead of waiting for the next poll\. Otherwise, if you have dedicated Amazon EC2 instances or Lambda functions constantly polling the EMR API for changes, you not only waste compute resources but might also reach your service quota\.

Following are a few cases when you might benefit by moving to an event\-driven architecture\.

## Case 1: Polling EMR using DescribeCluster API calls for Step Completion<a name="emr-service-limits-strategy-stepcompletion"></a>

**Example Polling EMR using DescribeCluster API calls for Step Completion**  
A common pattern is to submit a step to a running cluster and poll Amazon EMR for status about the step, typically using the DescribeCluster or DescribeStep APIs\. This task can also be accomplished with minimal delay by hooking into Amazon EMR Step Status Change event in ﻿Amazon CloudWatch Events or ﻿Amazon EventBridge\.  
This event includes the following information in its payload\.  

```
{
  "version": "0",
  "id": "999cccaa-eaaa-0000-1111-123456789012",
  "detail-type": "EMR Step Status Change",
  "source": "aws.emr",
  "account": "123456789012",
  "time": "2016-12-16T20:53:09Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "severity": "ERROR",
    "actionOnFailure": "CONTINUE",
    "stepId": "s-ZYXWVUTSRQPON",
    "name": "CustomJAR",
    "clusterId": "j-123456789ABCD",
    "state": "FAILED",
    "message": "Step s-ZYXWVUTSRQPON (CustomJAR) in Amazon EMR cluster j-123456789ABCD (Development Cluster) failed at 2016-12-16 20:53 UTC."
  }
}
```
In the detail map, a Lambda function could parse for "state", "stepId", or "clusterId" to find pertinent information\.

## Case 2: Polling EMR for Available Clusters to Run Workflows<a name="emr-service-limits-strategy-workflows"></a>

**Example Polling EMR for Available Clusters to Run Workflows**  
A pattern for customers who run multiple clusters is to run workflows on clusters as soon as they're available\. If there are many clusters running and a workflow needs to be performed on a cluster that's waiting, a pattern could be to poll EMR using DescribeCluster or ListClusters API calls for available clusters\. Another way to reduce the delay in knowing when a cluster is ready for a step, would be to process Amazon EMR Cluster State Change event in ﻿Amazon CloudWatch Events or ﻿Amazon EventBridge\.  
This event includes the following information in its payload\.  

```
{
  "version": "0",
  "id": "999cccaa-eaaa-0000-1111-123456789012",
  "detail-type": "EMR Cluster State Change",
  "source": "aws.emr",
  "account": "123456789012",
  "time": "2016-12-16T20:43:05Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "severity": "INFO",
    "stateChangeReason": "{\"code\":\"\"}",
    "name": "Development Cluster",
    "clusterId": "j-123456789ABCD",
    "state": "WAITING",
    "message": "Amazon EMR cluster j-123456789ABCD ..."
  }
}
```
For this event, a Lambda function could be set up to immediately send a waiting workflow to a cluster as soon as its status changes to WAITING\.

## Case 3: Polling EMR for Cluster Termination<a name="emr-service-limits-strategy-clustertermination"></a>

**Example Polling EMR for Cluster Termination**  
A common pattern of customers running many EMR clusters is polling Amazon EMR for terminated clusters so that work is no longer sent to it\. You can implement this pattern with the DescribeCluster and ListClusters API calls or by using Amazon EMR Cluster State Change event in ﻿Amazon CloudWatch Events or ﻿Amazon EventBridge\.  
Upon cluster termination, the event emitted looks like the following example\.  

```
{
  "version": "0",
  "id": "1234abb0-f87e-1234-b7b6-000000123456",
  "detail-type": "EMR Cluster State Change",
  "source": "aws.emr",
  "account": "123456789012",
  "time": "2016-12-16T21:00:23Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "severity": "INFO",
    "stateChangeReason": "{\"code\":\"USER_REQUEST\",\"message\":\"Terminated by user request\"}",
    "name": "Development Cluster",
    "clusterId": "j-123456789ABCD",
    "state": "TERMINATED",
    "message": "Amazon EMR Cluster jj-123456789ABCD (Development Cluster) has terminated at 2016-12-16 21:00 UTC with a reason of USER_REQUEST."
  }
}
```
The "detail" section of the payload includes the clusterId and state that can be acted on\.