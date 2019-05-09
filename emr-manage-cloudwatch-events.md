# Monitor CloudWatch Events<a name="emr-manage-cloudwatch-events"></a>

Amazon EMR tracks events and keeps information about them for up to seven days\. Changes in the state of clusters, instance groups, automatic scaling policies, and steps cause an event to be recorded\. Each event has information such as the date and time the event occurred, along with further detail about the event, such as the cluster or instance group affected\. 

The following table lists Amazon EMR events, along with the state or state change that the event indicates, the severity of the event, and event messages\. Each event is represented as a JSON object that is sent automatically to an event stream\. The JSON object includes further detail about the event\. The JSON object is particularly important when you set up rules for event processing using CloudWatch Events because rules seek to match patterns in the JSON object\. For more information, see [Events and Event Patterns](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html) and [Amazon EMR Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/EventTypes.html#emr-event-types) in the *Amazon CloudWatch Events User Guide*\.

## Cluster Events<a name="emr-cloudwatch-cluster-events"></a>


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  `STARTING`  |  `INFO`  |  Amazon EMR cluster *ClusterId \(ClusterName\)* was requested at *Time* and is being created\.   | 
|  `STARTING`  |  `INFO`  |   Applies only to clusters with the instance fleets configuration and multiple subnets selected within a VPC\.  Amazon EMR cluster *ClusterId \(ClusterName\)* is being created in subnet \(*SubnetName*\) in VPC \(*VPCName*\) in availability zone \(*AvailabilityZoneID*\), which was chosen from the specified VPC options\.  | 
|  `STARTING`  |  `INFO`  |   Applies only to clusters with the instance fleets configuration and multiple Availability Zones selected within EC2\-Classic\.  Amazon EMR cluster *ClusterId \(ClusterName\)* is being created in availability zone \(*AvailabilityZoneID*\), which was chosen from the specified availability zone options\.   | 
|  `RUNNING`  |  `INFO`  |  Amazon EMR cluster *ClusterId \(ClusterName\)* began running steps at *Time*\.  | 
|  `WAITING`  |  `INFO`  |  Amazon EMR cluster *ClusterId \(ClusterName\)* was created at *Time* and is ready for use\.  —or— Amazon EMR cluster *ClusterId \(ClusterName\)* finished running all pending steps at *Time*\. A cluster in the `WAITING` state may nevertheless be processing jobs\.   | 
|  `TERMINATED`  |  The severity depends on the reason for the state change, as shown in the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-manage-cloudwatch-events.html)  |  Amazon EMR Cluster *ClusterId \(ClusterName\)* has terminated at *Time* with a reason of *StateChangeReason:Code*\.  | 
|  `TERMINATED_WITH_ERRORS`  |  `CRITICAL`  |  Amazon EMR Cluster *ClusterId \(ClusterName\)* has terminated with errors at *Time* with a reason of *StateChangeReason:Code*\.  | 

## Instance Fleet Events<a name="emr-cloudwatch-instance-fleet-events"></a>

**Note**  
The instance fleets configuration is available only in Amazon EMR release versions 4\.8\.0 and later, excluding 5\.0\.0 and 5\.0\.3\.


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  From `PROVISIONING` to `WAITING`  |  `INFO`  |  Provisioning for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* is complete\. Provisioning started at *Time* and took *Num* minutes\. The instance fleet now has On\-Demand capacity of *Num* and Spot capacity of *Num*\. Target On\-Demand capacity was *Num*, and target Spot capacity was *Num*\.  | 
|  From `WAITING` to `RESIZING`  |  `INFO`  |  A resize for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* started at *Time*\. The instance fleet is resizing from an On\-Demand capacity of *Num* to a target of *Num*, and from a Spot capacity of *Num* to a target of *Num*\.  | 
|  From `RESIZING` to `WAITING`  |  `INFO`  |  The resizing operation for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* is complete\. The resize started at *Time* and took *Num* minutes\. The instance fleet now has On\-Demand capacity of *Num* and Spot capacity of *Num*\. Target On\-Demand capacity was *Num* and target Spot capacity was *Num*\.  | 
|  From `RESIZING` to `WAITING`  |  `WARN`  |  The resizing operation for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* has reached the timeout and stopped\. The resize started at *Time* and stopped after *Num* minutes\. The instance fleet now has On\-Demand capacity of *Num* and Spot capacity of *Num*\. Target On\-Demand capacity was *Num* and target Spot capacity was *Num*\.  | 
|  `ARRESTED`  |  `ERROR`  |  Instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was arrested at *Time* for the following reason: *ReasonDesc*\.  | 
|  `RESIZING`  |  `WARNING`  |  The resizing operation for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* is stuck for the following reason: *ReasonDesc*\.  | 
|  `WAITING` or `RUNNING`  |  `INFO`  |  A resize for instance fleet *InstanceFleetID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was initiated by *Entity* at *Time*\.  | 

## Instance Group Events<a name="emr-cloudwatch-instance-group-events"></a>


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  From `RESIZING` to `RUNNING`  |  `INFO`  |  The resizing operation for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* is complete\. It now has an instance count of *Num*\. The resize started at *Time* and took *Num* minutes to complete\.  | 
|  From `RUNNING` to `RESIZING`  |  `INFO`  |  A resize for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* started at *Time*\. It is resizing from an instance count of *Num* to *Num*\.  | 
|  `ARRESTED`  |  `ERROR`  |  Instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was arrested at *Time* for the following reason: *ReasonDesc*\.  | 
|  `RESIZING`  |  `WARNING`  |  The resizing operation for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* is stuck for the following reason: *ReasonDesc*\.  | 
|  `WAITING` or `RUNNING`  |  `INFO`  |  A resize for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was initiated by *Entity* at *Time*\.  | 

**Note**  
With Amazon EMR version 5\.21\.0 and later, you can override cluster configurations and specify additional configuration classifications for each instance group in a running cluster\. You do this by using the Amazon EMR console, the AWS Command Line Interface \(AWS CLI\), or the AWS SDK\. For more information, see [Supplying a Configuration for an Instance Group in a Running Cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-configure-apps-running-cluster.html)\.

The following table lists Amazon EMR events for the reconfiguration operation, along with the state or state change that the event indicates, the severity of the event, and event messages\. 


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  `RUNNING`  |  `INFO`  |  A reconfiguration for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* was initiated by user at *Time*\. Version of requested configuration is *Num*\.  | 
|  From `RECONFIGURING` to `RUNNING`  |  `INFO`  |  The reconfiguration operation for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* is complete\. The reconfiguration started at *Time* and took *Num* minutes to complete\. Current configuration version is *Num*\.  | 
|  From `RUNNING` to `RECONFIGURING`  |  `INFO`  |  A reconfiguration for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* started at *Time*\. It is configuring from version number *Num* to version number *Num*\.  | 
|  `RESIZING`  |  `INFO`  |  Reconfiguring operation towards configuration version *Num* for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* is temporarily blocked at *Time* because instance group is in *State*\.  | 
|  `RECONFIGURING`  |  `INFO`  | Resizing operation towards instance count Num for instance group InstanceGroupID in the Amazon EMR cluster ClusterId \(ClusterName\) is temporarily blocked at Time because the instance group is in State\. | 
|  `RECONFIGURING`  |  `WARNING`  |  The reconfiguration operation for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* failed at *Time* and took *Num* minutes to fail\. Failed configuration version is *Num*\.   | 
|  `RECONFIGURING`  |  `INFO`  |  Configurations are reverting to the previous successful version number *Num*for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time*\. New configuration version is *Num*\.   | 
|  From `RECONFIGURING` to `RUNNING`  |  `INFO`  |  Configurations were successfully reverted to the previous successful version *Num* for instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time*\. New configuration version is *Num*\.  | 
|  From `RECONFIGURING` to `ARRESTED`  |  `CRITICAL`  |  Failed to revert to the previous successful version *Num* for Instance group *InstanceGroupID* in the Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time*\.  | 

## Automatic Scaling Policy Events<a name="emr-cloudwatch-autoscale-events"></a>


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  `PENDING`  |  `INFO`  |  An Auto Scaling policy was added to instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time*\. The policy is pending attachment\. —or— The Auto Scaling policy for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was updated at *Time*\. The policy is pending attachment\.  | 
|  `ATTACHED`  |  `INFO`  |  The Auto Scaling policy for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was attached at *Time*\.  | 
|  `DETACHED`  |  `INFO`  |  The Auto Scaling policy for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* was detached at *Time*\.  | 
|  `FAILED`  |  `ERROR`  |  The Auto Scaling policy for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* could not attach and failed at *Time*\. —or— The Auto Scaling policy for instance group *InstanceGroupID* in Amazon EMR cluster *ClusterId \(ClusterName\)* could not detach and failed at *Time*\.  | 

## Step Events<a name="emr-cloudwatch-step-events"></a>


****  

| State or State Change | Severity | Message | 
| --- | --- | --- | 
|  `PENDING`  |  `INFO`  |  Step *StepID \(StepName\)* was added to Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time* and is pending execution\.   | 
|  `CANCEL_PENDING`  |  `WARN`  |  Step *StepID \(StepName\)* in Amazon EMR cluster *ClusterId \(ClusterName\)* was cancelled at *Time* and is pending cancellation\.   | 
|  `RUNNING`  |  `INFO`  |  Step *StepID \(StepName\)* in Amazon EMR cluster *ClusterId \(ClusterName\)* started running at *Time*\.   | 
|  `COMPLETED`  |  `INFO`  |  Step *StepID \(StepName\)* in Amazon EMR cluster *ClusterId \(ClusterName\)* completed execution at *Time*\. The step started running at *Time* and took *Num* minutes to complete\.  | 
|  `CANCELLED`  |  `WARN`  |  Cancellation request has succeeded for cluster step *StepID \(StepName\)* in Amazon EMR cluster *ClusterId \(ClusterName\)* at *Time*, and the step is now cancelled\.   | 
|  `FAILED`  |  `ERROR`  |  Step *StepID \(StepName\)* in Amazon EMR cluster *ClusterId \(ClusterName\)* failed at *Time*\.  | 

## Viewing Events Using the Amazon EMR Console<a name="emr-events-console"></a>

For each cluster, you can view a simple list of events in the details pane, which lists events in descending order of occurrence\. You can also view all events for all clusters in a region in descending order of occurrence\.

**Note**  
If you don't want a user to see all cluster events for a region, add a statement that denies permission \(`"Effect": "Deny"`\) for the `elasticmapreduce:ViewEventsFromAllClustersInConsole` action to a policy that is attached to the user\. 

**To view events for all clusters in a region**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Events**\.

**To view events for a particular cluster**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Cluster List**, select a cluster, and then choose **View details**\.

1. Choose **Events** in the cluster details pane\.  
![\[Amazon EMR events viewed in the cluster details pane.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/view-events-console.png)

## Creating Rules for Amazon EMR Events Using CloudWatch<a name="emr-events-cloudwatch-console"></a>

Amazon EMR automatically sends events to a CloudWatch event stream\. You can create rules that match events according to a specified pattern, and route the events to targets to take action, such as sending an email notification\. Patterns are matched against the event JSON object\. For more information about Amazon EMR event details, see [Amazon EMR Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/EventTypes.html#emr-event-types) in the *Amazon CloudWatch Events User Guide*\.

For information about setting up CloudWatch event rules, see [Creating a CloudWatch Rule That Triggers on an Event](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Rule.html)\.