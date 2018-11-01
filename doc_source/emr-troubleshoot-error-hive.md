# Hive Cluster Errors<a name="emr-troubleshoot-error-hive"></a>

 You can usually find the cause of a Hive error in the `syslog` file, which you link to from the **Steps** pane\. If you can't determine the problem there, check in the Hadoop task attempt error message\. Link to it on the **Task Attempts** pane\. 

The following errors are common to Hive clusters\.

**Topics**
+ [Are you using the latest version of Hive?](#emr-troubleshoot-error-hive-0)
+ [Did you encounter a syntax error in the Hive script?](#emr-troubleshoot-error-hive-1)
+ [Did a job fail when running interactively?](#emr-troubleshoot-error-hive-2)
+ [Are you having trouble loading data to or from Amazon S3 into Hive?](#emr-troubleshoot-error-hive-3)

## Are you using the latest version of Hive?<a name="emr-troubleshoot-error-hive-0"></a>

 The latest version of Hive has all the current patches and bug fixes and may resolve your issue\. 

## Did you encounter a syntax error in the Hive script?<a name="emr-troubleshoot-error-hive-1"></a>

 If a step fails, look at the `stdout` file of the logs for the step that ran the Hive script\. If the error is not there, look at the `syslog` file of the task attempt logs for the task attempt that failed\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

## Did a job fail when running interactively?<a name="emr-troubleshoot-error-hive-2"></a>

 If you are running Hive interactively on the master node and the cluster failed, look at the `syslog` entries in the task attempt log for the failed task attempt\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

## Are you having trouble loading data to or from Amazon S3 into Hive?<a name="emr-troubleshoot-error-hive-3"></a>

 If you are having trouble accessing data in Amazon S3, first check the possible causes listed in [Are you experiencing trouble loading data to or from Amazon S3?](emr-troubleshoot-errors-io.md#emr-troubleshoot-errors-io-1)\. If none of those issues is the cause, consider the following options specific to Hive\. 
+ Make sure you are using the latest version of Hive, which has all the current patches and bug fixes that may resolve your issue\. For more information, see [Apache Hive](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive.html)\.
+  Using `INSERT OVERWRITE` requires listing the contents of the Amazon S3 bucket or folder\. This is an expensive operation\. If possible, manually prune the path instead of having Hive list and delete the existing objects\. 
+ If you use Amazon EMR release versions earlier than 5\.0, you can use the following command in HiveQL to pre\-cache the results of an Amazon S3 list operation locally on the cluster:

  ```
  set hive.optimize.s3.query=true;
  ```
+  Use static partitions where possible\. 
+ In some versions of Hive and Amazon EMR, it is possible that using ALTER TABLES will fail because the table is stored in a different location than expected by Hive\. The solution is to add or update following in `/home/hadoop/conf/core-site.xml`:

  ```
  <property>
      <name>fs.s3n.endpoint</name>
      <value>s3.amazonaws.com</value>
  </property>
  ```