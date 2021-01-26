# Scaling Cluster Resources<a name="emr-scale-on-demand"></a>

You can adjust the number of Amazon EC2 instances available to an Amazon EMR cluster automatically or manually in response to workloads that have varying demands\. To use automatic scaling, you have two options\. You can enable EMR managed scaling or create a custom automatic scaling policy\. The following table describes the differences between the two options\.


|  | EMR managed scaling | Custom automatic scaling | 
| --- | --- | --- | 
|  Scaling policies and rules  |  No policy required\. EMR manages the automatic scaling activity by continuously evaluating cluster metrics and making optimized scaling decisions\.   |  You need to define and manage the automatic scaling policies and rules, such as the specific conditions that trigger scaling activities, evaluation periods, cooldown periods, etc\.  | 
|  Supported EMR release versions  |  Amazon EMR version 5\.30\.0 and later \(except Amazon EMR version 6\.0\.0\)  |  Amazon EMR version 4\.0\.0 and later  | 
|  Supported cluster composition  | Instance groups or instance fleets |  Instance groups only  | 
| Scaling limits configuration |  Scaling limits are configured for the entire cluster\.  |  Scaling limits can only be configured for each instance group\.  | 
|  Metrics evaluation frequency   |  Every 5 to 10 seconds More frequent evaluation of metrics allows EMR to make more precise scaling decisions\.  |  You can define the evaluation periods only in five\-minute increments\.  | 
|  Supported applications  |  Only YARN applications are supported, such as Spark, Hadoop, Hive, Flink\. Other applications, such as Presto, are currently not supported\.  |  You can choose which applications are supported when defining the automatic scaling rules\.   | 

**Considerations**
+ An Amazon EMR cluster always consists of one or three master nodes\. You can't scale the number of master nodes after you initially configure the cluster\. You can only scale core and task nodes in a cluster\. 
+ Reconfiguration and resizing of an instance group cannot occur at the same time\. If a reconfiguration is initiated while an instance group is resizing, then reconfiguration cannot start until the instance group has completed resizing, and the other way around\.

**Topics**
+ [Using EMR Managed Scaling in Amazon EMR](emr-managed-scaling.md)
+ [Using Automatic Scaling with a Custom Policy for Instance Groups](emr-automatic-scaling.md)
+ [Manually Resizing a Running Cluster](emr-manage-resize.md)
+ [Cluster Scale\-Down](emr-scaledown-behavior.md)