# Submit Hadoop Jobs Interactively<a name="interactive-jobs"></a>

In addition to adding steps to a cluster, you can connect to the master node using an SSH client or the AWS CLI and interactively submit Hadoop jobs\. For example, you can use PuTTY to establish an SSH connection with the master node and submit interactive Hive queries which are compiled into one or more Hadoop jobs\. 

You can submit Hadoop jobs interactively by establishing an SSH connection to the master node \(using an SSH client such as PuTTY or OpenSSH\) or by using the ssh subcommand in the AWS CLI\. You can submit jobs interactively to the master node even if you have 256 active steps running on the cluster\. Note however that log records associated with interactively submitted jobs are included in the "step created jobs" section of the currently running step's controller log\. For more information about step logs, see [View Log Files](emr-manage-view-web-log-files.md)\. 

The following examples demonstrate interactively submitting Hadoop jobs and Hive jobs to the master node\. The process for submitting jobs for other programming frameworks \(such as Pig\) is similar to these examples\. 

**To submit Hadoop jobs interactively using the AWS CLI**
+ You can submit Hadoop jobs interactively using the AWS CLI by establishing an SSH connection in the CLI command \(using the `ssh` subcommand\)\. ``To copy a JAR file from your local Windows machine to the master node's file system, type the following command\. Replace *j\-2A6HXXXXXXL7J* with your cluster ID, replace *mykey\.ppk* with the name of your key pair file, and replace *myjar\.jar* with the name of your JAR file\.

  ```
  aws emr put --cluster-id j-2A6HXXXXXXL7J --key-pair-file "C:\Users\username\Desktop\Keys\mykey.ppk" --src "C:\Users\username\myjar.jar"
  ```

  To create an SSH connection and submit the Hadoop job `myjar.jar`, type the following command\.

  ```
  aws emr ssh --cluster-id j-2A6HXXXXXXL7J --key-pair-file "C:\Users\username\Desktop\Keys\mykey.ppk" --command "hadoop jar myjar.jar"
  ```

For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**To interactively submit Hive jobs using the AWS CLI**

In addition to submitting jobs to the master node via JAR files, you can submit jobs by interacting with one of the Hadoop programming frameworks running on the master node\. For example, you can interactively submit Hive queries or Pig transformations at the command line, or you can submit scripts to the cluster for processing\. Your commands or scripts are then compiled into one or more Hadoop jobs\.``

The following procedure demonstrates running a Hive script using the AWS CLI\.

1. If Hive is not installed on the cluster, type the following command to install it\. Replace *j\-2A6HXXXXXXL7J* with your cluster ID\.

   ```
   aws emr install-applications --cluster-id j-2A6HXXXXXXL7J --apps Name=Hive 
   ```

1. Create a Hive script file containing the queries or commands to run\. The following example script named `my-hive.q` creates two tables, `aTable` and `anotherTable`, and copies the contents of `aTable` to `anotherTable`, replacing all data\. 

   ```
   1. ---- sample Hive script file: my-hive.q ----
   2. create table aTable (aColumn string) ;
   3. create table anotherTable like aTable;
   4. insert overwrite table anotherTable select * from aTable
   ```

1. Type the following commands to run the script from the command line using the `ssh` subcommand\. 

   To copy `my-hive.q` from a Windows machine to your cluster, type the following command\. Replace *j\-2A6HXXXXXXL7J* with your cluster ID and replace *mykey\.ppk* with the name of your key pair file\.

   ```
   aws emr put --cluster-id j-2A6HXXXXXXL7J --key-pair-file "C:\Users\username\Desktop\Keys\mykey.ppk" --src "C:\Users\username\my-hive.q"
   ```

   To create an SSH connection and submit the Hive script `my-hive.q`, type the following command\. 

   ```
   aws emr ssh --cluster-id j-2A6HXXXXXXL7J --key-pair-file "C:\Users\username\Desktop\Keys\mykey.ppk" --command "hive -f my-hive.q"
   ```

For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.