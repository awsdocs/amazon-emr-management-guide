# One\-click Access to Persistent Spark History Server<a name="app-history-spark-UI"></a>

With Amazon EMR versions 5\.25\.0 and later, you can connect to persistent Spark history server from the cluster **Summary** page or the **Application history** tab in the console\. The one\-click access to persistent Spark history server provides the following benefits: 
+ You can quickly analyze and troubleshoot active Spark jobs and job history without setting up a web proxy through an SSH connection\.
+ You can access persistent Spark history and relevant log files for active and terminated clusters\. The logs are available for 30 days after the Spark application ends\. 

## Logs Collection<a name="app-history-spark-UI-event-logs"></a>

To enable one\-click access to persistent Spark history server, Amazon EMR collects two types of logs: 
+ **Spark event logs** are collected into an EMR system bucket\. The event logs are encrypted at rest using Server\-Side Encryption with Amazon S3 Managed Keys \(SSE\-S3\)\. If you use a private subnet for your cluster, make sure to include `“arn:aws:s3:::prod.MyRegion.appinfo.src/*”` in the resource list of the Amazon S3 policy for the private subnet\. For more information, see [Minimum Amazon S3 Policy for Private Subnet](https://docs.aws.amazon.com/emr/latest/ManagementGuide/private-subnet-iampolicy.html)\.
+ **YARN container logs** are collected into an Amazon S3 bucket that you own\. You must enable logging for your cluster to access YARN container logs\. For more information, see [Configure Cluster Logging and Debugging](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-debugging.html)\.

If you need to disable this feature for privacy reasons, you can stop the daemon by using a bootstrap script when you create a cluster, as the following example demonstrates\.

```
aws emr create-cluster --name "Stop SparkUI Support" --release-label emr-5.29.0  
--applications Name=Hadoop Name=Spark --ec2-attributes KeyName=keyname 
--instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=CORE,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=TASK,InstanceCount=1,InstanceType=m3.xlarge 
--use-default-roles --bootstrap-actions Path=s3://elasticmapreduce/bootstrap-actions/run-if,Args=["instance.isMaster=true","echo Stop Spark UI | sudo tee /etc/apppusher/run-apppusher"]
```

After you run this bootstrap script, Amazon EMR will not collect any Spark event logs into the EMR system bucket\. No application history information will be available on the **Application history** tab, and you will lose access to the Spark history server UI from the console\. 

## Considerations and Limitations<a name="app-history-spark-UI-limitations"></a>

One\-click access to persistent Spark history server currently has the following limitations: 
+ This feature works only when the event log directory for Spark is in HDFS\. By default, Amazon EMR stores Spark event logs in a directory of HDFS\. If you change the default directory to a different file system, such as Amazon S3, this feature will not work\. 
+ This feature is currently not available for EMR clusters with multiple master nodes or for EMR clusters integrated with AWS Lake Formation\. 
+ To enable one\-click access to persistent Spark history server, you must have permission to the `ListSteps` action for EMR\. If you deny an IAM principal's permission to this action, it takes approximately five minutes for the permission change to propagate\.
+ If you reconfigure Spark applications in a running cluster, the application history will be not available through the Spark history server UI\. 
+ For each AWS account, the number of active Spark history server UIs cannot exceed 50\.
+ You can access Spark history server UI from the console in the US East \(N\.Virgina and Ohio\), US West \(N\.California and Oregon\), Canada \(Central\), EU \(Frankfurt, Ireland, and London\), and Asia Pacific \(Mumbai, Seoul, Singapore, Sydney, and Tokyo\) Regions\.

## Access Application History through Spark History Server UI<a name="app-history-spark-UI-steps"></a>

On the **Application history** tab or the cluster **Summary** page for your cluster in the Amazon EMR console, choose the **Spark history server UI** link\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/spark-history-server-UI-link.png)

The Spark history server UI opens in a new browser tab\. This web interface displays the same information as the open source Spark HistoryServer UI if you set up a web proxy through an SSH connection\. For more information, see [Monitoring and Instrumentation](https://spark.apache.org/docs/latest/monitoring.html)\.

You can view YARN container logs through the links on the Spark history server UI\. 

**Note**  
To access YARN container logs from the Spark history server UI, you must enable logging to Amazon S3 for your cluster\. If logging is not enabled, the links to YARN container logs will not work\. 