# Scaling Cluster Resources<a name="emr-scale-on-demand"></a>

You can adjust the number of Amazon EC2 instances available to an EMR cluster automatically or manually in response to workloads that have varying demands\. The following options are available:
+ Using Amazon EMR versions 4\.x and later, you can configure *automatic scaling* for the core instance group and task instance groups when you first create them or after the cluster is running\. Amazon EMR automatically configures Auto Scaling parameters according to rules you specify, and then adds and removes instances based on a CloudWatch metric\.
+ You can manually *resize* the core instance group and task instance groups by manually adding or removing Amazon EC2 instances\.
+ You can add a new task instance group to the cluster\.

The option to specify the Amazon EC2 instance type is only available during initial configuration of an instance group, so you can change the Amazon EC2 instance type only by adding a new task\. When using Amazon EMR version 5\.1\.0 or later, a cluster\-wide configuration allows you to specify whether Amazon EC2 instances removed from a cluster are terminated at the instance\-hour boundary, or when tasks on the Amazon EC2 instance are complete\. For more information, see [Cluster Scale\-Down](emr-scaledown-behavior.md)\.

Before you choose one of the methods for scaling described in this section, you should be familiar with some important concepts\. First, you should understand the role of *node types* in an EMR cluster and how *instance groups* are used to manage them\. For more information about the function of node types, see [What is Amazon EMR?](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html), and for more information about instance groups, see [Instance Groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/InstanceGroups.html)\. You should also develop a strategy for right\-sizing cluster resources based on the nature of your workload\. For more information, see [Cluster Configuration Guidelines](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-instances-guidelines.html)\. 

**Note**  
The master instance group in an EMR cluster always consists of a single node running on a single Amazon EC2 instance, so it can't scale after you initially configure it\. You work with the core instance groups and task instance groups to scale out and scale in a cluster\. It's possible to have a cluster with only a master node, and no core or task nodes\. You must have at least one core node at cluster creation in order to scale the cluster\. In other words, single node clusters cannot be resized\.  
Reconfiguring and resizing an instance group cannot occur at the same time\. If a reconfiguration is initiated while an instance group is resizing, reconfiguration cannot start until the instance group has completed resizing, and vice versa\.

**Topics**
+ [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)
+ [Manually Resizing a Running Cluster](emr-manage-resize.md)
+ [Cluster Scale\-Down](emr-scaledown-behavior.md)