# Step 1: Gather Data About the Issue<a name="emr-troubleshoot-failed-1"></a>

 The first step in troubleshooting a cluster is to gather information about what went wrong and the current status and configuration of the cluster\. This information will be used in the following steps to confirm or rule out possible causes of the issue\. 

## Define the Problem<a name="emr-troubleshoot-failed-1-problem"></a>

 A clear definition of the problem is the first place to begin\. Some questions to ask yourself: 
+  What did I expect to happen? What happened instead? 
+  When did this problem first occur? How often has it happened since? 
+  Has anything changed in how I configure or run my cluster? 

## Cluster Details<a name="emr-troubleshoot-failed-1-cluster"></a>

 The following cluster details are useful in helping track down issues\. For more information on how to gather this information, see [View Cluster Status and Details](emr-manage-view-clusters.md)\. 
+  Identifier of the cluster\. \(Also called a job flow identifier\.\) 
+  Region and availability zone the cluster was launched into\. 
+  State of the cluster, including details of the last state change\. 
+  Type and number of EC2 instances specified for the master, core, and task nodes\. 