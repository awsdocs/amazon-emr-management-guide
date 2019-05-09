# Cluster Scale\-Down<a name="emr-scaledown-behavior"></a>

With Amazon EMR release version 5\.1\.0 and later, there are two options for scale\-down behavior: terminate at the instance\-hour boundary for Amazon EC2 billing, or terminate at task completion\. Starting with Amazon EMR release version 5\.10\.0, the setting for termination at instance\-hour boundary is deprecated because of the introduction of per\-second billing in Amazon EC2\. We do not recommend specifying termination at the instance\-hour boundary in versions where the option is available\.

**Warning**  
If you use the AWS CLI to issue a `modify-instance-groups` with `EC2InstanceIdsToTerminate`, these instances are terminated immediately, without consideration for these settings, and regardless of the status of applications running on them\. Terminating an instance in this way risks data loss and unpredictable cluster behavior\.

When terminate at task completion is specified, Amazon EMR blacklists and drains tasks from nodes before terminating the Amazon EC2 instances\. With either behavior specified, Amazon EMR does not terminate Amazon EC2 instances in core instance groups if it could lead to HDFS corruption\. 

## Terminate at Task Completion<a name="emr-scaledown-terminate-task"></a>

Amazon EMR allows you to scale down your cluster without affecting your workload\. Amazon EMR gracefully decommissions YARN, HDFS, and other daemons on core and task nodes during a resize down operation without losing data or interrupting jobs\. Amazon EMR only shrinks instance groups if the work assigned to the groups has completed and they are idle\. For YARN NodeManager decommissioning, you can manually adjust the time a node waits for decommissioning\.

This time is set using a property in the `yarn-site` configuration classification\. Using Amazon EMR release version 5\.12\.0 and later, specify the `yarn.resourcemanager.nodemanager-graceful-decommission-timeout-secs` property\. Using earlier Amazon EMR release versions, specify the `yarn.resourcemanager.decommissioning.timeout` property\.

If there are still running containers or YARN applications when the decommissioning timeout passes, the node is forced to be decommissioned and YARN reschedules affected containers on other nodes\. The default value is 3600s \(one hour\)\. You can set this timeout to be an arbitrarily high value to force graceful shrink to wait longer\. For more information, see [Graceful Decommission of YARN Nodes](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/GracefulDecommission.html#configuration) in Apache Hadoop documentation\.

### Task Node Groups<a name="emr-scaledown-task-nodes"></a>

Amazon EMR intelligently selects instances that are not running tasks related to any step or application, and removes them from a cluster first\. If all instances in the cluster are being used, Amazon EMR waits for tasks to complete on a given instance before removing it from the cluster\. The default wait time is 1 hour and this value can be changed by setting `yarn.resourcemanager.decommissioning.timeout`\. Amazon EMR dynamically uses the new setting\. You can set this to an arbitrarily large number to ensure that no tasks are killed while shrinking the cluster\.

### Core Node Groups<a name="emr-scaledown-core-nodes"></a>

 On core nodes, both YARN NodeManager and HDFS DataNode daemons must be decommissioned in order for the instance group to shrink\. For YARN, graceful shrink ensures that a node marked for decommissioning is only transitioned to the `DECOMMISSIONED` state if there are no pending or incomplete containers or applications\. The decommissioning finishes immediately if there are no running containers on the node at the beginning of decommissioning\. 

For HDFS, graceful shrink ensures that the target capacity of HDFS is large enough to fit all existing blocks\. If the target capacity is not large enough, only a partial amount of core instances are decommissioned such that the remaining nodes can handle the current data residing in HDFS\. You should ensure additional HDFS capacity to allow further decommissioning\. You should also try to minimize write I/O before attempting to shrink instance groups as that may delay the completion of the resize operation\. 

Another limit is the default replication factor, `dfs.replication` inside `/etc/hadoop/conf/hdfs-site`\. Amazon EMR configures the value based on the number of instances in the cluster: `1` with 1\-3 instances, `2` for clusters with 4\-9 instances, and `3` for clusters with 10\+ instances\. Graceful shrink does not allow you to shrink core nodes below the HDFS replication factor; this is to prevent HDFS from being unable to close files due insufficient replicas\. To circumvent this limit, you must lower the replication factor and restart the NameNode daemon\. 

## Configuring Amazon EMR Scale\-Down Behavior<a name="emr-scaledown-configure"></a>

**Note**  
This configuration feature is only available for Amazon EMR releases 5\.1\.0 or later\.

You can use the AWS Management Console, the AWS CLI, or the Amazon EMR API to configure scale\-down behavior when you create a cluster\. Configuring scale\-down using the AWS Management Console is done in the **Step 3: General Cluster Settings** screen when you create a cluster using **Advanced options**\.

![\[Scale-down configuration for Amazon EMR instance termination.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/auto-scaling-scaledown.png)

When you create a cluster using the AWS CLI, use the `--ScaleDownBehavior` option to specify either `TERMINATE_AT_INSTANCE_HOUR` or `TERMINATE_AT_TASK_COMPLETION`\.