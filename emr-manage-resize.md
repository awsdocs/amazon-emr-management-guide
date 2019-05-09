# Manually Resizing a Running Cluster<a name="emr-manage-resize"></a>

You can add and remove instances from core and task instance groups and instance fleets in a running cluster using the AWS Management Console, AWS CLI, or the Amazon EMR API\. If a cluster uses instance groups, you explicitly change the instance count\. If your cluster uses instance fleets, you can change the target units for On\-Demand Instances and Spot Instances\. The instance fleet then adds and removes instances to meet the new target\. For more information, see [Instance Fleet Options](emr-instance-fleet.md#emr-instance-fleet-options)\. Applications can use newly provisioned Amazon EC2 instances to host nodes as soon as the instances are available\. When instances are removed, Amazon EMR terminates tasks in a way that does not interrupt jobs and safeguards against data loss\. For more information, see [Terminate at Task Completion](emr-scaledown-behavior.md#emr-scaledown-terminate-task)\.

## Resize a Cluster Using the Console<a name="resize-console"></a>

You can use the Amazon EMR console to resize a running cluster\.

**To change the instance count for an existing running cluster using the console**

1. From the **Cluster List** page, choose a cluster to resize\.

1. On the **Cluster Details** page, choose **Hardware**\.

1. If your cluster uses instance groups, choose **Resize** in the **Instance count** column for the instance group that you want to resize, type a new instance count, and then select the green check mark\.  
![\[Resize instance groups for an Amazon EMR cluster\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/resize-instance-group.png)

   **–OR–**

   If your cluster uses instance fleets, choose **Resize** in the **Provisioned capacity** column, type new values for **On\-demand units** and **Spot units**, and then choose **Resize**\.  
![\[Resize instance fleets for an Amazon EMR cluster\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/resize-instance-fleet.png)

When you make a change to the number of nodes, the **Status** of the instance group updates\. When the change you requested is complete, the **Status** is **Running**\.

## Resize a Cluster Using the AWS CLI<a name="ResizingParameters"></a>

You can use the AWS CLI to resize a running cluster\. You can increase or decrease the number of task nodes, and you can increase the number of core nodes in a running cluster\. It is also possible to terminate an instance in the core instance group using the AWS CLI or the API\. This should be done with caution\. Terminating an instance in the core instance group risks data loss, and the instance is not automatically replaced\.

In addition to resizing the core and task groups, you can also add one or more task instance groups to a running cluster using the AWS CLI\. <a name="IncreaseDecreaseNodesawscli"></a>

**To resize a cluster by changing the instance count using the AWS CLI**

You can add instances to the core group or task group, and you can remove instances from the task group using the AWS CLI `modify-instance-groups` subcommand with the `InstanceCount` parameter\. To add instances to the core or task groups, increase the `InstanceCount`\. To reduce the number of instances in the task group, decrease the `InstanceCount`\. Changing the instance count of the task group to 0 removes all instances but not the instance group\.
+ To increase the number of instances in the task instance group from 3 to 4, type the following command and replace *ig\-31JXXXXXXBTO* with the instance group ID\.

  ```
  aws emr modify-instance-groups --instance-groups InstanceGroupId=ig-31JXXXXXXBTO,InstanceCount=4
  ```

  To retrieve the `InstanceGroupId`, use the `describe-cluster` subcommand\. The output is a JSON object called `Cluster` that contains the ID of each instance group\. To use this command, you need the cluster ID \(which you can retrieve using the `aws emr list-clusters` command or the console\)\. To retrieve the instance group ID, type the following command and replace *j\-2AXXXXXXGAPLF* with the cluster ID\.

  ```
  aws emr describe-cluster --cluster-id j-2AXXXXXXGAPLF
  ```

  Using the AWS CLI, you can also terminate an instance in the core instance group with the `--modify-instance-groups` subcommand\.
**Warning**  
Specifying `EC2InstanceIdsToTerminate` must be done with caution\. Instances are terminated immediately, regardless of the status of applications running on them, and the instance is not automatically replaced\. This is true regardless of the cluster's **Scale down behavior** configuration\. Terminating an instance in this way risks data loss and unpredictable cluster behavior\.

  To terminate a specific instance you need the instance group ID \(returned by the `aws emr describe-cluster --cluster-id` subcommand\) and the instance ID \(returned by the `aws emr list-instances --cluster-id` subcommand\), type the following command, replace *ig\-6RXXXXXX07SA* with the instance group ID and replace *i\-f9XXXXf2* with the instance ID\.

  ```
  1. aws emr modify-instance-groups --instance-groups InstanceGroupId=ig-6RXXXXXX07SA,EC2InstanceIdsToTerminate=i-f9XXXXf2
  ```

  For more information about using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**To resize a cluster by adding task instance groups using the AWS CLI**

Using the AWS CLI, you can add from 1–48 task instance groups to a cluster with the `--add-instance-groups` subcommand\. Task instances groups can only be added to a cluster containing a master instance group and a core instance group\. When using the AWS CLI, you can add up to five task instance groups each time you use the `--add-instance-groups` subcommand\.

1. To add a single task instance group to a cluster, type the following command and replace *j\-JXBXXXXXX37R* with the cluster ID\.

   ```
   1. aws emr add-instance-groups --cluster-id j-JXBXXXXXX37R --instance-groups InstanceCount=6,InstanceGroupType=task,InstanceType=m4.large
   ```

1. To add multiple task instance groups to a cluster, type the following command and replace *j\-JXBXXXXXX37R* with the cluster ID\. You can add up to five task instance groups in a single command\.

   ```
   aws emr add-instance-groups --cluster-id j-JXBXXXXXX37R --instance-groups InstanceCount=6,InstanceGroupType=task,InstanceType=m4.large InstanceCount=10,InstanceGroupType=task,InstanceType=m4.large
   ```

   For more information about using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Interrupting a Resize<a name="interruptible-resize"></a>

Using Amazon EMR version 4\.1\.0 or later, you can issue a resize in the midst of an existing resize operation\. Additionally, you can stop a previously submitted resize request or submit a new request to override a previous request without waiting for it to finish\. You can also stop an existing resize from the console or using the `ModifyInstanceGroups` API call with the current count as the target count of the cluster\.

The following screenshot shows a task instance group that is resizing but can be stopped by choosing **Stop**\.

![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/resize-stop.png)

**To interrupt a resize using the AWS CLI**

You can use the AWS CLI to stop a resize by using the `modify-instance-groups` subcommand\. Assume that you have six instances in your instance group and you want to increase this to 10\. You later decide that you would like to cancel this request:
+ The initial request:

  ```
  aws emr modify-instance-groups --instance-groups InstanceGroupId=ig-myInstanceGroupId,InstanceCount=10
  ```

  The second request to stop the first request:

  ```
  aws emr modify-instance-groups --instance-groups InstanceGroupId=ig-myInstanceGroupId,InstanceCount=6
  ```

**Note**  
Because this process is asynchronous, you may see instance counts change with respect to previous API requests before subsequent requests are honored\. In the case of shrinking, it is possible that if you have work running on the nodes, the instance group may not shrink until nodes have completed their work\.

## Arrested State<a name="emr-manage-resizeArrested"></a>

An instance group goes into an arrested state if it encounters too many errors while trying to start the new cluster nodes\. For example, if new nodes fail while performing bootstrap actions, the instance group goes into an *ARRESTED* state, rather than continuously provisioning new nodes\. After you resolve the underlying issue, reset the desired number of nodes on the cluster's instance group, and then the instance group resumes allocating nodes\. Modifying an instance group instructs Amazon EMR to attempt to provision nodes again\. No running nodes are restarted or terminated\.

In the AWS CLI, the `list-instances` subcommand returns all instances and their states as does the `describe-cluster` subcommand\. If Amazon EMR detects a fault with an instance group, it changes the group's state to `ARRESTED`\. 

**To reset a cluster in an ARRESTED state using the AWS CLI**

Type the `describe-cluster` subcommand with the `--cluster-id` parameter to view the state of the instances in your cluster\.
+ To view information on all instances and instance groups in a cluster, type the following command and replace *j\-3KVXXXXXXY7UG* with the cluster ID\.

  ```
  1. aws emr describe-cluster --cluster-id j-3KVXXXXXXY7UG
  ```

  The output displays information about your instance groups and the state of the instances:

  ```
   1. {
   2.     "Cluster": {
   3.         "Status": {
   4.             "Timeline": {
   5.                 "ReadyDateTime": 1413187781.245,
   6.                 "CreationDateTime": 1413187405.356
   7.             },
   8.             "State": "WAITING",
   9.             "StateChangeReason": {
  10.                 "Message": "Waiting after step completed"
  11.             }
  12.         },
  13.         "Ec2InstanceAttributes": {
  14.             "Ec2AvailabilityZone": "us-west-2b"
  15.         },
  16.         "Name": "Development Cluster",
  17.         "Tags": [],
  18.         "TerminationProtected": false,
  19.         "RunningAmiVersion": "3.2.1",
  20.         "NormalizedInstanceHours": 16,
  21.         "InstanceGroups": [
  22.             {
  23.                 "RequestedInstanceCount": 1,
  24.                 "Status": {
  25.                     "Timeline": {
  26.                         "ReadyDateTime": 1413187775.749,
  27.                         "CreationDateTime": 1413187405.357
  28.                     },
  29.                     "State": "RUNNING",
  30.                     "StateChangeReason": {
  31.                         "Message": ""
  32.                     }
  33.                 },
  34.                 "Name": "MASTER",
  35.                 "InstanceGroupType": "MASTER",
  36.                 "InstanceType": "m4.large",
  37.                 "Id": "ig-3ETXXXXXXFYV8",
  38.                 "Market": "ON_DEMAND",
  39.                 "RunningInstanceCount": 1
  40.             },
  41.             {
  42.                 "RequestedInstanceCount": 1,
  43.                 "Status": {
  44.                     "Timeline": {
  45.                         "ReadyDateTime": 1413187781.301,
  46.                         "CreationDateTime": 1413187405.357
  47.                     },
  48.                     "State": "RUNNING",
  49.                     "StateChangeReason": {
  50.                         "Message": ""
  51.                     }
  52.                 },
  53.                 "Name": "CORE",
  54.                 "InstanceGroupType": "CORE",
  55.                 "InstanceType": "m4.large",
  56.                 "Id": "ig-3SUXXXXXXQ9ZM",
  57.                 "Market": "ON_DEMAND",
  58.                 "RunningInstanceCount": 1
  59.             }
  60. ...
  61. }
  ```

  To view information about a particular instance group, type the `list-instances` subcommand with the `--cluster-id` and `--instance-group-types` parameters\. You can view information for the MASTER, CORE, or TASK groups\.

  ```
  1. aws emr list-instances --cluster-id j-3KVXXXXXXY7UG --instance-group-types "CORE"
  ```

  Use the `modify-instance-groups` subcommand with the `--instance-groups` parameter to reset a cluster in the `ARRESTED` state\. The instance group id is returned by the `describe-cluster` subcommand\.

  ```
  1. aws emr modify-instance-groups --instance-groups InstanceGroupId=ig-3SUXXXXXXQ9ZM,InstanceCount=3
  ```