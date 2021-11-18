# Control cluster termination<a name="emr-plan-termination"></a>

This section describes your options for shutting down Amazon EMR clusters\. It covers auto\-termination and termination protection, and how they interact with other Amazon EMR features\.

You can shut down an Amazon EMR cluster in the following ways:
+ **Termination after last step execution** \- Create a transient cluster that shuts down after all steps complete\.
+ **Auto\-termination \(after idle\)** \- Create a cluster with an auto\-termination policy that shuts down after a specified idle time\. For more information, see [Using an auto\-termination policy](emr-auto-termination-policy.md)\.
+ **Manual termination** \- Create a long\-running cluster that continues to run until you terminate it deliberately\. For information about how to terminate a cluster manually, see [Terminate a cluster](UsingEMR_TerminateJobFlow.md)\.

You can also set termination protection on a cluster to avoid shutting down EC2 instances by accident or error\.

When Amazon EMR shuts down your cluster, all Amazon EC2 instances in the cluster shut down\. Data in the instance store and EBS volumes is no longer available and not recoverable\. Understanding and managing cluster termination is critical to developing a strategy to manage and preserve data by writing to Amazon S3 and balancing cost\. 

**Topics**
+ [Configuring a cluster to continue or terminate after step execution](emr-plan-longrunning-transient.md)
+ [Using an auto\-termination policy](emr-auto-termination-policy.md)
+ [Using termination protection](UsingEMR_TerminationProtection.md)