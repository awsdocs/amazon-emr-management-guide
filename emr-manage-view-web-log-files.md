# View Log Files<a name="emr-manage-view-web-log-files"></a>

 Amazon EMR and Hadoop both produce log files that report status on the cluster\. By default, these are written to the master node in the /mnt/var/log/ directory\. Depending on how you configured your cluster when you launched it, these logs may also be archived to Amazon S3 and may be viewable through the graphical debugging tool\. 

 There are many types of logs written to the master node\. Amazon EMR writes step, bootstrap action, and instance state logs\. Apache Hadoop writes logs to report the processing of jobs, tasks, and task attempts\. Hadoop also records logs of its daemons\. For more information about the logs written by Hadoop, go to [http://hadoop\.apache\.org/docs/stable/hadoop\-project\-dist/hadoop\-common/ClusterSetup\.html](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)\. 

**Topics**
+ [View Log Files on the Master Node](#emr-manage-view-web-log-files-master-node)
+ [View Log Files Archived to Amazon S3](#emr-manage-view-web-log-files-s3)
+ [View Log Files in the Debugging Tool](#emr-manage-view-web-log-files-debug)

## View Log Files on the Master Node<a name="emr-manage-view-web-log-files-master-node"></a>

The following table lists some of the log files you'll find on the master node\.


| Location | Description | 
| --- | --- | 
|  /mnt/var/log/bootstrap\-actions  | Logs written during the processing of the bootstrap actions\. | 
|  /mnt/var/log/hadoop\-state\-pusher  | Logs written by the Hadoop state pusher process\. | 
|  /mnt/var/log/instance\-controller \(Amazon EMR 4\.6\.0 and earlier\) /emr/instance\-controller \(Amazon EMR 4\.7\.0 and later\)  | Instance controller logs\. | 
|  /mnt/var/log/instance\-state  | Instance state logs\. These contain information about the CPU, memory state, and garbage collector threads of the node\. | 
|  /mnt/var/log/service\-nanny \(Amazon EMR 4\.6\.0 and earlier\) /emr/service\-nanny \(Amazon EMR 4\.7\.0 and later\)  | Logs written by the service nanny process\. | 
|  /mnt/var/log/*application*  | Logs specific to an application such as Hadoop, Spark, or Hive\. | 
|  /mnt/var/log/hadoop/steps/*N*  | Step logs that contain information about the processing of the step\. The value of *N* indicates the stepId assigned by Amazon EMR\. For example, a cluster has two steps: `s-1234ABCDEFGH` and `s-5678IJKLMNOP`\. The first step is located in `/mnt/var/log/hadoop/steps/s-1234ABCDEFGH/` and the second step in `/mnt/var/log/hadoop/steps/s-5678IJKLMNOP/`\.  The step logs written by Amazon EMR are as follows\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-manage-view-web-log-files.html)  | 

**To view log files on the master node**

1.  Use SSH to connect to the master node as described in [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. 

1.  Navigate to the directory that contains the log file information you wish to view\. The preceding table gives a list of the types of log files that are available and where you will find them\. The following example shows the command for navigating to the step log with an ID, `s-1234ABCDEFGH`\. 

   ```
   cd /mnt/var/log/hadoop/steps/s-1234ABCDEFGH/
   ```

1. Use a file viewer of your choice to view the log file\. The following example uses the Linux `less` command to view the `controller` log file\.

   ```
   less controller
   ```

## View Log Files Archived to Amazon S3<a name="emr-manage-view-web-log-files-s3"></a>

By default, Amazon EMR clusters launched using the console automatically archive log files to Amazon S3\. You can specify your own log path, or you can allow the console to automatically generate a log path for you\. For clusters launched using the CLI or API, you must configure Amazon S3 log archiving manually\. 

 When Amazon EMR is configured to archive log files to Amazon S3, it stores the files in the S3 location you specified, in the /*JobFlowId*/ folder, where *JobFlowId* is the cluster identifier\. 

The following table lists some of the log files you'll find on Amazon S3\.


| Location | Description | 
| --- | --- | 
|  /*JobFlowId*/node/  | Node logs, including bootstrap action, instance state, and application logs for the node\. The logs for each node are stored in a folder labeled with the identifier of the EC2 instance of that node\. | 
|  /*JobFlowId*/node/*instanceId*/*application*  | The logs created by each application or daemon associated with an application\. For example, the Hive server log is located at `JobFlowId/node/instanceId/hive/hive-server.log`\. | 
|  /*JobFlowId*/steps/*N*/  | Step logs that contain information about the processing of the step\. The value of *N* indicates the stepId assigned by Amazon EMR\. For example, a cluster has two steps: `s-1234ABCDEFGH` and `s-5678IJKLMNOP`\. The first step is located in `/mnt/var/log/hadoop/steps/s-1234ABCDEFGH/` and the second step in `/mnt/var/log/hadoop/steps/s-5678IJKLMNOP/`\.  The step logs written by Amazon EMR are as follows\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-manage-view-web-log-files.html)  | 
|  /*JobFlowId*/containers  |  Application container logs\. The logs for each YARN application are stored in these locations\.  | 

**To view log files archived to Amazon S3 using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1.  Open the S3 bucket specified when you configured the cluster to archive log files in Amazon S3\. 

1.  Navigate to the log file containing the information to display\. The preceding table gives a list of the types of log files that are available and where you will find them\. 

1.  Double\-click on a log file to view it in the browser\. 

 If you don't want to view the log files in the Amazon S3 console, you can download the files from Amazon S3 to your local machine using a tool such as the Amazon S3 Organizer plug\-in for the Firefox web browser, or by writing an application to retrieve the objects from Amazon S3\. For more information, see [Getting Objects](https://docs.aws.amazon.com/AmazonS3/latest/dev/GettingObjectsUsingAPIs.html) in the *Amazon Simple Storage Service Developer Guide*\. 

## View Log Files in the Debugging Tool<a name="emr-manage-view-web-log-files-debug"></a>

 Amazon EMR does not automatically enable the debugging tool\. You must configure this when you launch the cluster\. 

**To view cluster logs using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1.  From the **Cluster List** page, choose the details icon next to the cluster you want to view\. 

    This brings up the **Cluster Details** page\. In the **Steps** section, the links to the right of each step display the various types of logs available for the step\. These logs are generated by Amazon EMR\. 

1.  To view a list of the Hadoop jobs associated with a given step, choose the **View Jobs** link to the right of the step\. 

1.  To view a list of the Hadoop tasks associated with a given job, choose the **View Tasks** link to the right of the job\.   
![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-view-jobs-newcon.png)

1.  To view a list of the attempts a given task has run while trying to complete, choose the **View Attempts** link to the right of the task\.   
![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-view-tasks-newcon.png)

1.  To view the logs generated by a task attempt, choose the **stderr**, **stdout**, and **syslog** links to the right of the task attempt\.   
![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-view-attempts-log-newcon.png)

 The debugging tool displays links to the log files after Amazon EMR uploads the log files to your bucket on Amazon S3\. Because log files are uploaded to Amazon S3 every 5 minutes, it can take a few minutes for the log file uploads to complete after the step completes\. 

 Amazon EMR periodically updates the status of Hadoop jobs, tasks, and task attempts in the debugging tool\. You can click **Refresh List** in the debugging panes to get the most up\-to\-date status of these items\. 