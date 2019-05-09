# Too many fetch\-failures<a name="emr-troubleshoot-error-resource-1"></a>

The presence of "**Too many fetch\-failures**" or "**Error reading task output**" error messages in step or task attempt logs indicates the running task is dependent on the output of another task\. This often occurs when a reduce task is queued to execute and requires the output of one or more map tasks and the output is not yet available\. 

There are several reasons the output may not be available: 
+ The prerequisite task is still processing\. This is often a map task\. 
+ The data may be unavailable due to poor network connectivity if the data is located on a different instance\. 
+ If HDFS is used to retrieve the output, there may be an issue with HDFS\. 

The most common cause of this error is that the previous task is still processing\. This is especially likely if the errors are occurring when the reduce tasks are first trying to run\. You can check whether this is the case by reviewing the syslog log for the cluster step that is returning the error\. If the syslog shows both map and reduce tasks making progress, this indicates that the reduce phase has started while there are map tasks that have not yet completed\. 

One thing to look for in the logs is a map progress percentage that goes to 100% and then drops back to a lower value\. When the map percentage is at 100%, this does not mean that all map tasks are completed\. It simply means that Hadoop is executing all the map tasks\. If this value drops back below 100%, it means that a map task has failed and, depending on the configuration, Hadoop may try to reschedule the task\. If the map percentage stays at 100% in the logs, look at the CloudWatch metrics, specifically `RunningMapTasks`, to check whether the map task is still processing\. You can also find this information using the Hadoop web interface on the master node\. 

If you are seeing this issue, there are several things you can try:
+ Instruct the reduce phase to wait longer before starting\. You can do this by altering the Hadoop configuration setting mapred\.reduce\.slowstart\.completed\.maps to a longer time\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md)\. 
+ Match the reducer count to the total reducer capability of the cluster\. You do this by adjusting the Hadoop configuration setting mapred\.reduce\.tasks for the job\. 
+ Use a combiner class code to minimize the amount of outputs that need to be fetched\. 
+ Check that there are no issues with the Amazon EC2 service that are affecting the network performance of the cluster\. You can do this using the [Service Health Dashboard](http://status.aws.amazon.com//)\. 
+ Review the CPU and memory resources of the instances in your cluster to make sure that your data processing is not overwhelming the resources of your nodes\. For more information, see [Configure Cluster Hardware and Networking](emr-plan-instances.md)\. 
+ Check the version of the Amazon Machine Image \(AMI\) used in your Amazon EMR cluster\. If the version is 2\.3\.0 through 2\.4\.4 inclusive, update to a later version\. AMI versions in the specified range use a version of Jetty that may fail to deliver output from the map phase\. The fetch error occurs when the reducers cannot obtain output from the map phase\.

  Jetty is an open\-source HTTP server that is used for machine to machine communications within a Hadoop cluster\.