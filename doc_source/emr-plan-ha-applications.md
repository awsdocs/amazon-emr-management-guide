# Supported Applications and Features<a name="emr-plan-ha-applications"></a>

This topic provides information about the Hadoop high availability features of HDFS NameNode and YARN ResourceManager in an EMR cluster, and how the high availability features work with open source applications and other EMR features\.

## High Availability HDFS<a name="emr-plan-ha-applications-HDFS"></a>

An EMR cluster with multiple master nodes enables the HDFS NameNode high availability feature in Hadoop\. For more information, see [HDFS High Availability](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html)\.

In an EMR cluster, NameNode runs only on two of the three master nodes\. One NameNode is in an `active` state and the other is in a `standby` state\. If the master node with `active` NameNode fails, EMR starts an automatic HDFS failover process\. The master node with `standby` NameNode becomes `active` and takes over all client operations in the cluster\. EMR replaces the failed master node with a new one, which then rejoins as a `standby`\.

If you need to find out which NameNode is `active`, you can use SSH to connect to any master node in the cluster and run the following command:

```
hdfs haadmin -getAllServiceState
```

The output lists the two nodes where NameNode is installed and their status\. For example,

```
ip-##-#-#-##1.ec2.internal:8020 active
ip-##-#-#-##2.ec2.internal:8020 standby
```

## High Availability YARN ResourceManager<a name="emr-plan-ha-applications-YARN"></a>

An EMR cluster with multiple master nodes enables the YARN ResourceManager high availability feature in Hadoop\. For more information, see [ResourceManager High Availability](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)\.

In an EMR cluster with multiple master nodes, YARN ResourceManager runs on all three master nodes\. One ResourceManager is in `active` state, and the other two are in `standby` state\. If the master node with `active` ResourceManager fails, EMR starts an automatic failover process\. A master node with a `standby` ResourceManager takes over all operations\. EMR replaces the failed master node with a new one, which then rejoins the ResourceManager quorum as a `standby`\.

You can connect to “http://*master\-public\-dns\-name*:8088/cluster” for any master node, which automatically directs you to the `active` resource manager\. To find out which resource manager is `active`, use SSH to connect to any master node in the cluster\. Then run the following command to get a list of the three master nodes and their status:

```
yarn rmadmin -getAllServiceState
```

## Supported Applications in an EMR Cluster with Multiple Master Nodes<a name="emr-plan-ha-applications-list"></a>

You can install and run the following applications on an EMR cluster with multiple master nodes\. For each application, the master node failover process varies\. 


| Application | Availability during master node failover | Notes | 
| --- | --- | --- | 
| Flink | Availability not affected by master node failover | Flink jobs on Amazon EMR run as YARN applications\. Flink's JobManagers run as YARN's ApplicationMasters on core nodes\. The JobManager is not affected by the master node failover process\.  If you use Amazon EMR version 5\.27\.0 or earlier, the JobManager is a single point of failure\. When the JobManager fails, it loses all job states and will not resume the running jobs\. You can enable JobManager high availability by configuring application attempt count, checkpointing, and enabling ZooKeeper as state storage for Flink\. For more information, see [Configuring Flink on an EMR Cluster with Multiple Master Nodes](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/flink-configure.html#flink-multi-master)\. Beginning with Amazon EMR version 5\.28\.0, no manual configuration is needed to enable JobManager high availability\. | 
| Ganglia | Availability not affected by master node failover | Ganglia is available on all master nodes, so Ganglia can continue to run during the master node failover process\. | 
| Hadoop | High availability |  HDFS NameNode and YARN ResourceManager automatically fail over to the standby node when the active master node fails\.  | 
| HBase |  High availability  | HBase automatically fails over to the standby node when the active master node fails\.  If you are connecting to HBase through a REST or Thrift server, you must switch to a different master node when the active master node fails\. | 
| HCatalog |  Availability not affected by master node failover  | HCatalog is built upon Hive metastore, which exists outside of the cluster\. HCatalog remains available during the master node failover process\. | 
| JupyterHub | High availability |  JupyterHub is installed on all three master instances\. It is highly recommended to configure notebook persistence to prevent notebook loss upon master node failure\. For more information, see [Configuring Persistence for Notebooks in Amazon S3](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-jupyterhub-s3.html)\.  | 
| Livy | High availability |  Livy is installed on all three master nodes\. When the active master node fails, you lose access to the current Livy session and need to create a new Livy session on a different master node or on the new replacement node\.   | 
| Mahout |  Availability not affected by master node failover  | Since Mahout has no daemon, it is not affected by the master node failover process\. | 
| MXNet |  Availability not affected by master node failover  | Since MXNet has no daemon, it is not affected by the master node failover process\. | 
| Phoenix |  High Availability   | Phoenix’ QueryServer runs only on one of the three master nodes\. Phoenix on all three masters is configured to connect the Phoenix QueryServer\. You can find the private IP of Phoenix’s Query server by using `/etc/phoenix/conf/phoenix-env.sh` file | 
| Pig |  Availability not affected by master node failover  | Since Pig has no daemon, it is not affected by the master node failover process\. | 
| Spark | High availability | All Spark applications run in YARN containers and can react to master node failover in the same way as high availability YARN features\. | 
| Sqoop | High availability | By default, sqoop\-job and sqoop\-metastore store data\(job descriptions\) on local disk of master that runs the command, if you want to save metastore data on external Database, please refer to apache Sqoop documentation | 
| Tez |  High availability  | Since Tez containers run on YARN, Tez behaves the same way as YARN during the master node failover process\. | 
| TensorFlow |  Availability not affected by master node failover  |  Since TensorFlow has no daemon, it is not affected by the master node failover process\. | 
| Zeppelin |  High availability  | Zeppelin is installed on all three master nodes\. Zeppelin stores notes and interpreter configurations in HDFS by default to prevent data loss\. Interpreter sessions are completely isolated across all three master instances\. Session data will be lost upon master failure\. It is recommended to not modify the same note concurrently on different master instances\. | 
| ZooKeeper | High availability |  ZooKeeper is the foundation of the HDFS automatic failover feature\. ZooKeeper provides a highly available service for maintaining coordination data, notifying clients of changes in that data, and monitoring clients for failures\. For more information, see [HDFS Automatic Failover](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Automatic_Failover)\.  | 

To run the following applications in an EMR cluster with multiple master nodes, you must configure an external database\. The external database exists outside the cluster and makes data persistent during the master node failover process\. For the following applications, the service components will automatically recover during the master node failover process, but active jobs may fail and need to be retried\.


| Application | Availability during master node failover | Notes | 
| --- | --- | --- | 
| Hive | High availability for service components only |  An external metastore for Hive is required\. For more information, see [Configuring an External Metastore for Hive](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-metastore-external-hive.html)\.  | 
| Hue | High availability for service components only |  An external database for Hue is required\. For more information, see [Using Hue with a Remote Database in Amazon RDS](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/hue-rds.html)\.  | 
| Oozie |  High availability for service components only  | An external database for Oozie is required\. For more information, see [Using Oozie with a Remote Database in Amazon RDS](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/oozie-rds.html)\. Oozie\-server is installed on only one master node, while oozie\-client is installed on all three master nodes\. The oozie\-clients are configured to connect to the correct oozie\-server by default\. You can find the private DNS name of the master node where oozie\-server is installed by checking the variable `OOZIE_URL` on any master node in the file `/etc/oozie/conf.dist/oozie-client-env.sh`\. | 
| Presto |  High availability for service components only  | An external Hive metastore for Presto is required\. You can use [Presto with the AWS Glue Data Catalog](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-presto-glue.html) or [use an External MySQL Database for Hive](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-hive-metastore-external.html)\.  | 

**Note**  
When a master node fails, your Java Database Connectivity \(JDBC\) or Open Database Connectivity \(ODBC\) terminates its connection to the master node\. You can connect to any of the remaining master nodes to continue your work because the Hive metastore daemon runs on all master nodes\. Or you can wait for the failed master node to be replaced\.

## How EMR Features work in a Cluster with Multiple Master Nodes<a name="emr-plan-ha-features"></a>

### Connecting to Master Nodes Using SSH<a name="emr-plan-ha-features-SSH"></a>

You can connect to any of the three master nodes in an EMR cluster using SSH in the same way you connect to a single master node\. For more information, see [Connect to the Master Node Using SSH](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-connect-master-node-ssh.html)\.

If a master node fails, your SSH connection to that master node ends\. To continue your work, you can connect to one of the other two master nodes\. Alternatively, you can access the new master node after EMR replaces the failed one with a new one\.

**Note**  
The private IP address for the replacement master node remains the same as the previous one\. The public IP address for the replacement master node may change\. You can retrieve the new IP addresses in the console or by using the `describe-cluster` command in the AWS CLI\.  
NameNode only runs on two of the master nodes\. However, you can run `hdfs` CLI commands and operate jobs to access HDFS on all three master nodes\.

### Working with Steps in an EMR Cluster with Multiple Master Nodes<a name="emr-plan-ha-features-steps"></a>

You can submit steps to an EMR cluster with multiple master nodes in the same way you work with steps in a cluster with a single master node\. For more information, see [Submit Work to a Cluster](https://docs.aws.amazon.com/emr/latest/ManagementGuide/AddingStepstoaJobFlow.html)\. 

The following are considerations for working with steps in an EMR cluster with multiple master nodes:
+ If a master node fails, the steps that are running on the master node are marked as FAILED\. Any data that were written locally are lost\. However, the status FAILED may not reflect the real state of the steps\.
+ If a running step has started a YARN application when the master node fails, the step can continue and succeed due to the automatic failover of the master node\.
+ It is recommended that you check the status of steps by referring to the output of the jobs\. For example, MapReduce jobs use a `_SUCCESS` file to determine if the job completes successfully\.
+ It is recommended that you set ActionOnFailure parameter to CONTINUE, or CANCEL\_AND\_WAIT, instead of TERMINATE\_JOB\_FLOW, or TERMINATE\_CLUSTER\.

### Unsupported Features in an EMR Cluster with Multiple Master Nodes<a name="emr-plan-ha-features-unsupported"></a>

The following EMR features are currently not available in an EMR cluster with multiple master nodes:
+ EMR Notebooks
+ Instance fleets
+ One\-click access to persistent Spark history server

**Note**  
 To use Kerberos authentication in your cluster, you must configure an external KDC\.  
Beginning with Amazon EMR version 5\.27\.0, you can configure HDFS Transparent encryption on an EMR cluster with multiple master nodes\. For more information, see [Transparent Encryption in HDFS on Amazon EMR](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-encryption-tdehdfs.html)\.