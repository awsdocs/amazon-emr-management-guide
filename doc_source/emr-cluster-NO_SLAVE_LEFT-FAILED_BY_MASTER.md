# Cluster Terminates With NO\_SLAVE\_LEFT and Core Nodes FAILED\_BY\_MASTER<a name="emr-cluster-NO_SLAVE_LEFT-FAILED_BY_MASTER"></a>

Usually, this happens because termination protection is disabled, and all core nodes exceed disk storage capacity as specified by a maximum utilization threshold in the `yarn-site` configuration classification, which corresponds to the `yarn-site.xml` file\. This value is 90% by default\. When disk utilization for a core node exceeds the utilization threshold, the YARN NodeManager health service reports the node as `UNHEALTHY`\. While it's in this state, Amazon EMR blacklists the node and does not allocate YARN containers to it\. If the node remains unhealthy for 45 minutes, Amazon EMR marks the associated Amazon EC2 instance for termination as `FAILED_BY_MASTER`\. When all Amazon EC2 instances associated with core nodes are marked for termination, the cluster terminates with the status `NO_SLAVE_LEFT` because there are no resources to execute jobs\.

Exceeding disk utilization on one core node might lead to a chain reaction\. If a single node exceeds the disk utilization threshold because of HDFS, other nodes are likely to be near the threshold as well\. The first node exceeds the disk utilization threshold, so Amazon EMR blacklists it\. This increases the burden of disk utilization for remaining nodes because they begin to replicate HDFS data among themselves that they lost on the blacklisted node\. Each node subsequently goes `UNHEALTHY` in the same way, and the cluster eventually terminates\.

## Best Practices and Recommendations<a name="w19aac25c33c23b7b7"></a>

### Configure Cluster Hardware with Adequate Storage<a name="w19aac25c33c23b7b7b3"></a>

When you create a cluster, make sure that there are enough core nodes and that each has an adequate instance store and EBS storage volumes for HDFS\. For more information, see [Calculating the Required HDFS Capacity of a Cluster](emr-plan-instances-guidelines.md#emr-plan-instances-hdfs)\. You can also add core instances to existing instance groups manually or by using auto\-scaling\. The new instances have the same storage configuration as other instances in the instance group\. For more information, see [Scaling Cluster Resources](emr-scale-on-demand.md)\.

### Enable Termination Protection<a name="w19aac25c33c23b7b7b5"></a>

Enable termination protection\. This way, if a core node is blacklisted, you can connect to the associated Amazon EC2 instance using SSH to troubleshoot and recover data\. If you enable termination protection, be aware that Amazon EMR does not replace the Amazon EC2 instance with a new instance\. For more information, see [Using Termination Protection](UsingEMR_TerminationProtection.md)\.

### Create an Alarm for the MRUnhealthyNodes CloudWatch Metric<a name="w19aac25c33c23b7b7b7"></a>

This metric reports the number of nodes reporting an `UNHEALTHY` status\. It's equivalent to the YARN metric `mapred.resourcemanager.NoOfUnhealthyNodes`\. You can set up a notification for this alarm to warn you of unhealthy nodes before the 45\-minute timeout is reached\. For more information, see [Monitor Metrics with CloudWatch](UsingEMR_ViewingMetrics.md)\.

### Tweak Settings Using yarn\-site<a name="w19aac25c33c23b7b7b9"></a>

The settings below can be adjusted according to your application requirements\. For example, you may want to increase the disk utilization threshold where a node reports `UNHEALTHY` by increasing the value of `yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage`\.

You can set these values when you create a cluster using the `yarn-site` configuration classification\. For more information see [Configuring Applications](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-configure-apps.html) in the *Amazon EMR Release Guide*\. You can also connect to the Amazon EC2 instances associated with core nodes using SSH, and then add the values in `/etc/hadoop/conf.empty/yarn-site.xml` using a text editor\. After making the change, you must restart hadoop\-yarn\-nodemanager as shown below\.

**Important**  
When you restart the NodeManager service, active YARN containers are killed unless `yarn.nodemanager.recovery.enabled` is set to `true` using the `yarn-site` configuration classification when you create the cluster\. You must also specify the directory in which to store container state using the `yarn.nodemanager.recovery.dir` property\.

```
sudo /sbin/stop hadoop-yarn-nodemanager
sudo /sbin/start hadoop-yarn-nodemanager
```

For more information about current `yarn-site` properties and default values, see [YARN default settings](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-common/yarn-default.xml) in Apache Hadoop documentation\.


| Property | Default Value | Description | 
| --- | --- | --- | 
|  yarn\.nodemanager\.disk\-health\-checker\.interval\-ms  |  120000  |  The frequency \(in seconds\) that the disk health checker runs\.  | 
|  yarn\.nodemanager\.disk\-health\-checker\.min\-healthy\-disks  |  0\.25  |  The minimum fraction of the number of disks that must be healthy for NodeManager to launch new containers\. This correspond to both yarn\.nodemanager\.local\-dirs \(by default, `/mnt/yarn` in Amazon EMR\) and yarn\.nodemanager\.log\-dirs \(by default `/var/log/hadoop-yarn/containers`, which is symlinked to `mnt/var/log/hadoop-yarn/containers` in Amazon EMR\)\.  | 
|  `yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage`  |  90\.0  |  The maximum percentage of disk space utilization allowed after which a disk is marked as bad\. Values can range from 0\.0 to 100\.0\. If the value is greater than or equal to 100, the NodeManager checks for a full disk\. This applies to `yarn-nodemanager.local-dirs` and `yarn.nodemanager.log-dirs`\.  | 
|  `yarn.nodemanager.disk-health-checker.min-free-space-per-disk-mb`  |  0  |  The minimum space that must be available on a disk for it to be used\. This applies to `yarn-nodemanager.local-dirs` and `yarn.nodemanager.log-dirs`\.  | 