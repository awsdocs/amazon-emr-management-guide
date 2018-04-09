# Control Cluster Termination<a name="emr-manage-termination"></a>

Control over cluster termination is determined by two options: termination protection and auto\-termination\. By default, when you launch a cluster using the console, termination protection is turned on\. This prevents accidental termination of the cluster\. When you launch a cluster using the CLI or API, termination protection is turned off\. 

Auto\-termination determines whether the cluster should automatically terminate when all steps are complete\. When you launch a cluster using the console, the default behavior is for the cluster to remain active after all steps are complete\. In other words, the cluster is long\-running\. A long\-running cluster must be manually terminated\. When you launch a cluster using the CLI or API, the default behavior is for the cluster to terminate when data processing is complete; that is, when no more steps are left to run\. This creates a transient cluster\. 

**Topics**
+ [Terminate a Cluster](UsingEMR_TerminateJobFlow.md)
+ [Managing Cluster Termination](UsingEMR_TerminationProtection.md)