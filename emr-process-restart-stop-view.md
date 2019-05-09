# Viewing and Restarting Amazon EMR and Application Processes \(Daemons\)<a name="emr-process-restart-stop-view"></a>

When you troubleshoot a cluster, you may want to list running processes\. You may also find it useful to stop or restart processes in some circumstances—for example, after you change a configuration or notice a problem with a particular process after you analyze log files and error messages\.

There are two types of processes that run on a cluster: Amazon EMR processes \(for example, instance\-controller and Log Pusher\), and processes associated with the applications installed on the cluster \(for example, hadoop\-hdfs\-namenode, and hadoop\-yarn\-resourcemanager\)\.

To work with processes directly on a cluster, you connect to the master node\. For more information, see [Connect to the Cluster](emr-connect-master-node.md)\.

## Viewing Running Processes<a name="emr-process-view"></a>

If you are using Amazon EMR version 4\.x or later, application releases are packaged using a system based on Apache Bigtop, so these application processes are configured via \.conf scripts under the upstart init system\. Amazon EMR processes, on the other hand, are configured using SysV \(init\.d scripts\) which is backwards compatible with upstart\.

## To view a list of running Amazon EMR processes
+ Type the following command \(without the `$`, which indicates the Linux command prompt\):

  ```
  $  ls /etc/init.d/
  ```

  The command returns a list of running Amazon EMR processes similar to the following example:

  ```
  acpid          cloud-init-local          instance-controller          ntpd
  ```

## To view a list of processes associated with application releases
+ Type the following command:

  ```
  $  ls /etc/init/
  ```

  The command returns a list of running application processes similar to the following example:

  ```
  control-alt-delete.conf          hadoop-yarn-resourcemanager.conf                hive-metastore.conf 
  ```

## Restarting Processes<a name="emr-process-restart"></a>

After you determine which processes are running, you can stop and then restart them if necessary\. How you start and stop a service depends on whether it's an Amazon EMR service or a service associated with an application\.

## To restart a process associated with an application release

1. Type the following command to stop the process, replacing `processname` with the process name returned by the `ls `command in the procedure above:

   ```
   $  sudo /etc/init.d/processname stop
   ```

   For example: `sudo /etc/init.d/hadoop-hdfs-namenode stop`

1. Type the following command to restart the process:

   ```
   $  sudo /etc/init.d/processname start
   ```

   For example, `sudo /etc/init.d/hadoop-hdfs-namenode start`\.

## To restart an Amazon EMR process

1. Type the following command to stop the process, replacing `processname` with the process name returned by the `ls `command in the procedure above:

   ```
   $  sudo /sbin/stop processname
   ```

   For example, `sudo /sbin/stop instance-controller`\.

1. Type the following command to restart the process:

   ```
   $  sudo sbin/start processname
   ```

   For example, `sudo sbin/start instance-controller`\.
**Note**  
The `sbin/start, stop` and `restart` commands are symlinks to `/sbin/intictl`\. For more information about `initctl`, see the initctl man page by typing `man initctl` at the command prompt\.