# Add More than 256 Steps to a Cluster<a name="AddMoreThan256Steps"></a>

Beginning with AMI 3\.1\.1 \(Hadoop 2\.x\) and AMI 2\.4\.8 \(Hadoop 1\.x\), you can submit an unlimited number of steps over the lifetime of a long\-running cluster, but only 256 can be active or pending at any given time\. For earlier AMI versions, the total number of steps that can be processed by a cluster is limited to 256 \(including system steps such as install Hive and install Pig\)\. For more information, see [Submit Work to a Cluster](AddingStepstoaJobFlow.md)\.

You can use one of several methods to overcome the 256 step limit in pre\-3\.1\.1 and pre\-2\.4\.8 AMIs: 

1. Have each step submit several jobs to Hadoop\. This does not allow you unlimited steps in pre\-3\.1\.1 and pre\-2\.4\.8 AMIs, but it is the easiest solution if you need a fixed number of steps greater than 256\.

1. Write a workflow program that runs in a step on a long\-running cluster and submits jobs to Hadoop\. You could have the workflow program either:
   + Listen to an Amazon SQS queue to receive information about new steps to run\.
   + Check an Amazon S3 bucket on a regular schedule for files containing information about the new steps to run\.

1. Write a workflow program that runs on an EC2 instance outside of Amazon EMR and submits jobs to your long\-running clusters using SSH\.

1. Connect to your long\-running cluster via SSH and submit Hadoop jobs using the Hadoop API\. For more information, see [http://hadoop\.apache\.org/docs/current/api/org/apache/hadoop/mapred/JobClient\.html](http://hadoop.apache.org/docs/current/api/org/apache/hadoop/mapred/JobClient.html)\.

1. Connect to the master node using an SSH client \(such as PuTTY or OpenSSH\) and manually submit jobs to the cluster or use the `ssh` subcommand in the AWS CLI to both connect and submit jobs\. For more information about establishing an SSH connection with the master node, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. For more information about interactively submitting Hadoop jobs, see [Submit Hadoop Jobs Interactively](interactive-jobs.md)\.