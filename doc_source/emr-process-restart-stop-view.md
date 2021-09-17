# Viewing and restarting Amazon EMR and application processes \(daemons\)<a name="emr-process-restart-stop-view"></a>

When you troubleshoot a cluster, you may want to list running processes\. You may also find it useful to stop or restart processes in some circumstances\. For example, you can restart a process after you change a configuration or notice a problem with a particular process after you analyze log files and error messages\.

There are two types of processes that run on a cluster: Amazon EMR processes \(for example, instance\-controller and Log Pusher\), and processes associated with the applications installed on the cluster \(for example, hadoop\-hdfs\-namenode, and hadoop\-yarn\-resourcemanager\)\.

To work with processes directly on a cluster, you must first connect to the master node\. For more information, see [Connect to the cluster](emr-connect-master-node.md)\.

## Viewing running processes<a name="emr-process-view"></a>

The method you use to view running processes on a cluster differs according to the Amazon EMR version you use\. 

------
#### [ EMR 5\.30\.0 and 6\.0\.0 and later ]

**Example : List all running processes**  
The following example uses `systemctl` and specifies `--type` to view all processes\.  

```
systemctl --type=service
```

**Example : List specific processes**  
The following example lists all processes with names that contain `hadoop`\.  

```
systemctl --type=service | grep -i hadoop
```
Example output:  

```
 hadoop-hdfs-namenode.service           loaded active running Hadoop namenode
 hadoop-httpfs.service                  loaded active running Hadoop httpfs
 hadoop-kms.service                     loaded active running Hadoop kms
 hadoop-mapreduce-historyserver.service loaded active running Hadoop historyserver
 hadoop-state-pusher.service            loaded active running Daemon process that processes and serves EMR metrics data.
 hadoop-yarn-proxyserver.service        loaded active running Hadoop proxyserver
 hadoop-yarn-resourcemanager.service    loaded active running Hadoop resourcemanager
 hadoop-yarn-timelineserver.service     loaded active running Hadoop timelineserver
```

**Example : See a detailed status report for a specific process**  
The following example displays a detailed status report for the `hadoop-hdfs-namenode` service\.  

```
sudo systemctl status hadoop-hdfs-namenode
```
Example output:  

```
hadoop-hdfs-namenode.service - Hadoop namenode
   Loaded: loaded (/etc/systemd/system/hadoop-hdfs-namenode.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-08-18 21:01:46 UTC; 26min ago
 Main PID: 9733 (java)
    Tasks: 0
   Memory: 1.1M
   CGroup: /system.slice/hadoop-hdfs-namenode.service
           ‣ 9733 /etc/alternatives/jre/bin/java -Dproc_namenode -Xmx1843m -server -XX:OnOutOfMemoryError=kill -9 %p ...

Aug 18 21:01:37 ip-172-31-20-123 systemd[1]: Starting Hadoop namenode...
Aug 18 21:01:37 ip-172-31-20-123 su[9715]: (to hdfs) root on none
Aug 18 21:01:37 ip-172-31-20-123 hadoop-hdfs-namenode[9683]: starting namenode, logging to /var/log/hadoop-hdfs/ha...out
Aug 18 21:01:46 ip-172-31-20-123 hadoop-hdfs-namenode[9683]: Started Hadoop namenode:[  OK  ]
Aug 18 21:01:46 ip-172-31-20-123 systemd[1]: Started Hadoop namenode.
Hint: Some lines were ellipsized, use -l to show in full.
```

------
#### [ EMR 4\.x \- 5\.29\.0 ]

**Example : List all running processes**  
The following example lists all running processes\.  

```
initctl list
```

------
#### [ EMR 2\.x \- 3\.x ]

**Example : List all running processes**  
The following example lists all running processes\.  

```
ls /etc/init.d/
```

------

## Stopping and restarting processes<a name="emr-process-restart"></a>

After you determine which processes are running, you can stop and then restart them if necessary\.

------
#### [ EMR 5\.30\.0 and 6\.0\.0 and later ]

**Example : Stop a process**  
The following example stops the `hadoop-hdfs-namenode` process\.  

```
sudo systemctl stop hadoop-hdfs-namenode
```
You can query the `status` to verify that the process is stopped\.  

```
sudo systemctl status hadoop-hdfs-namenode
```
Example output:  

```
hadoop-hdfs-namenode.service - Hadoop namenode
  Loaded: loaded (/etc/systemd/system/hadoop-hdfs-namenode.service; enabled; vendor preset: disabled)
  Active: failed (Result: exit-code) since Wed 2021-08-18 21:37:50 UTC; 8s ago
Main PID: 9733 (code=exited, status=143)
```

**Example : Start a process**  
The following example starts the `hadoop-hdfs-namenode` process\.  

```
sudo systemctl start hadoop-hdfs-namenode
```
You can query the status to verify that the process is running\.  

```
sudo systemctl status hadoop-hdfs-namenode
```
Example output:  

```
hadoop-hdfs-namenode.service - Hadoop namenode
   Loaded: loaded (/etc/systemd/system/hadoop-hdfs-namenode.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-08-18 21:38:24 UTC; 2s ago
  Process: 13748 ExecStart=/etc/init.d/hadoop-hdfs-namenode start (code=exited, status=0/SUCCESS)
 Main PID: 13800 (java)
    Tasks: 0
   Memory: 1.1M
   CGroup: /system.slice/hadoop-hdfs-namenode.service
           ‣ 13800 /etc/alternatives/jre/bin/java -Dproc_namenode -Xmx1843m -server -XX:OnOutOfMemoryError=kill -9 %p...
```

------
#### [ EMR 4\.x \- 5\.29\.0 ]

**Example : Stop a running process**  
The following example stops the `hadoop-hdfs-namenode` service\.   

```
sudo stop hadoop-hdfs-namenode
```

**Example : Restart a stopped process**  
The following example restarts the `hadoop-hdfs-namenode` service\. You must use the `start` command and not `restart`\.  

```
sudo start hadoop-hdfs-namenode
```

**Example : Check the process status**  
The following fetches the status for `hadoop-hdfs-namenode`\. You can use the `status` command to verify that the process has stopped or started\.   

```
sudo status hadoop-hdfs-namenode
```

------
#### [ EMR 2\.x \- 3\.x ]

**Example : Stop an application process**  
The following example stops the `hadoop-hdfs-namenode` service, which is associated with the version of Amazon EMR installed on the cluster\.  

```
sudo /etc/init.d/hadoop-hdfs-namenode stop
```

**Example : Restart an application process**  
The following example command restarts the `hadoop-hdfs-namenode` process:  

```
sudo /etc/init.d/hadoop-hdfs-namenode start
```

**Example : Stop an Amazon EMR process**  
The following example stops a process, such as instance\-controller, which is not associated with the version of Amazon EMR on the cluster\.  

```
sudo /sbin/stop instance-controller
```

**Example : Restart an Amazon EMR process**  
The following example restarts a process, such as instance\-controller, which is not associated with the version of Amazon EMR on the cluster\.  

```
sudo /sbin/start instance-controller
```

**Note**  
The `/sbin/start, stop` and `restart` commands are symlinks to `/sbin/intictl`\. For more information about `initctl`, see the initctl man page by typing `man initctl` at the command prompt\.

------