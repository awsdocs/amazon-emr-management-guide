# Add More than 256 Steps to a Cluster<a name="AddMoreThan256Steps"></a>

With AMI 3\.1\.1 \(Hadoop 2\.x\) or later and AMI 2\.4\.8 \(Hadoop 1\.x\) or later, you can submit an unlimited number of steps over the lifetime of a long\-running cluster\. However, only 256 steps can be PENDING or ACTIVE at any given time\. For earlier AMI versions, the total number of steps that can be processed by a cluster is limited to 256, including system steps such as install Hive and install Pig\. For more information, see [Submit Work to a Cluster](AddingStepstoaJobFlow.md)\.

You can use one of several methods to overcome the 256\-step limit in AMI versions earler than 3\.1\.1 and 2\.4\.8: 

1. Have each step submit several jobs to Hadoop\. This does not allow you unlimited steps in AMI versions earlier than 3\.1\.1 and 2\.4\.8, but it is the easiest solution if you need a fixed number of steps greater than 256\.

1. Write a workflow program that runs in a step on a long\-running cluster and submits jobs to Hadoop\. The workflow program can do one of the following:
   + Listen to an Amazon SQS queue to receive information about new steps to run\.
   + Check an Amazon S3 bucket on a regular schedule for files containing information about the new steps to run\.

1. Write a workflow program that runs on an Amazon EC2 instance outside Amazon EMR and submits jobs to your long\-running clusters using SSH\.

1. Connect to your long\-running cluster via SSH and submit Hadoop jobs using the Hadoop API\. For more information, see [http://hadoop\.apache\.org/docs/current/api/org/apache/hadoop/mapred/JobClient\.html](http://hadoop.apache.org/docs/current/api/org/apache/hadoop/mapred/JobClient.html)\.

1. Connect to the master node and submit jobs to the cluster\. You can connect using an SSH client, such as PuTTY or OpenSSH, and manually submit jobs to the cluster, or you can use the `ssh` subcommand in the AWS CLI to both connect and submit jobs\. For more information about establishing an SSH connection with the master node, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. For more information about interactively submitting Hadoop jobs, see [Submit Hadoop Jobs Interactively](interactive-jobs.md)\.