# View Persistent Application User Interfaces<a name="app-history-spark-UI"></a>

Starting with Amazon EMR version 5\.25\.0, you can connect to the persistent Spark History Server application details hosted off\-cluster using the cluster **Summary** page or the **Application user interfaces** tab in the console\. Tez UI and YARN timeline server persistent application interfaces are available starting with Amazon EMR version 5\.30\.1\. One\-click link access to persistent application history provides the following benefits: 
+ You can quickly analyze and troubleshoot active jobs and job history without setting up a web proxy through an SSH connection\.
+ You can access application history and relevant log files for active and terminated clusters\. The logs are available for 30 days after the application ends\. 

On the **Application user interfaces** tab or the cluster **Summary** page for your cluster in the Amazon EMR 5\.30\.1 or 6\.x console, choose the **YARN timeline server**, **Tez UI**, or **Spark history server** link\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/persistent-application-UI-links.png)

The Application UI opens in a new browser tab\. For more information, see [Monitoring and Instrumentation](https://spark.apache.org/docs/latest/monitoring.html)\.

You can view YARN container logs through the links on the Spark history server, YARN timeline server, and Tez UI\. 

**Note**  
To access YARN container logs from the Spark history server, YARN timeline server, and Tez UI, you must enable logging to Amazon S3 for your cluster\. If logging is not enabled, the links to YARN container logs will not work\. 

## Logs Collection<a name="app-history-spark-UI-event-logs"></a>

To enable one\-click access to persistent application user interfaces, Amazon EMR collects two types of logs: 
+ **Application event logs** are collected into an EMR system bucket\. The event logs are encrypted at rest using Server\-Side Encryption with Amazon S3 Managed Keys \(SSE\-S3\)\. If you use a private subnet for your cluster, make sure to include `“arn:aws:s3:::prod.MyRegion.appinfo.src/*”` in the resource list of the Amazon S3 policy for the private subnet\. For more information, see [Minimum Amazon S3 Policy for Private Subnet](https://docs.aws.amazon.com/emr/latest/ManagementGuide/private-subnet-iampolicy.html)\.
+ **YARN container logs** are collected into an Amazon S3 bucket that you own\. You must enable logging for your cluster to access YARN container logs\. For more information, see [Configure Cluster Logging and Debugging](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-debugging.html)\.

If you need to disable this feature for privacy reasons, you can stop the daemon by using a bootstrap script when you create a cluster, as the following example demonstrates\.

```
aws emr create-cluster --name "Stop Application UI Support" --release-label emr-5.33.0  
--applications Name=Hadoop Name=Spark --ec2-attributes KeyName=keyname 
--instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=CORE,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=TASK,InstanceCount=1,InstanceType=m3.xlarge 
--use-default-roles --bootstrap-actions Path=s3://elasticmapreduce/bootstrap-actions/run-if,Args=["instance.isMaster=true","echo Stop Application UI | sudo tee /etc/apppusher/run-apppusher; sudo systemctl stop apppusher || exit 0"]
```

After you run this bootstrap script, Amazon EMR will not collect any Spark History Server or YARN timeline server event logs into the EMR system bucket\. No application history information will be available on the **Application user interfaces** tab, and you will lose access to all application user interfaces from the console\.

## Considerations and Limitations<a name="app-history-spark-UI-limitations"></a>

One\-click access to persistent application user interfaces currently has the following limitations: 
+ There will be at least a two\-minute delay when the application details show up on the Spark History Server UI\.
+ This feature works only when the event log directory for the application is in HDFS\. By default, Amazon EMR stores event logs in a directory of HDFS\. If you change the default directory to a different file system, such as Amazon S3, this feature will not work\. 
+ This feature is currently not available for EMR clusters with multiple master nodes or for EMR clusters integrated with AWS Lake Formation\. 
+ To enable one\-click access to persistent application user interfaces, you must have permission to the `DescribeCluster` action for EMR\. If you deny an IAM principal's permission to this action, it takes approximately five minutes for the permission change to propagate\.
+ If you reconfigure applications in a running cluster, the application history will be not available through the application UI\. 
+ For each AWS account, the number of active application UIs cannot exceed 50\.
+ You can access application UIs from the console in the US East \(N\.Virginia and Ohio\), US West \(N\.California and Oregon\), Canada \(Central\), EU \(Frankfurt, Ireland, and London\), Asia Pacific \(Mumbai, Seoul, Singapore, Sydney, and Tokyo\), China \(Beijing\) operated by Sinnet, and China \(Ningxia\) operated by NWCD Regions\.