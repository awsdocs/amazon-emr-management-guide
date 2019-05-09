# Troubleshoot a Slow Cluster<a name="emr-troubleshoot-slow"></a>

 This section walks you through the process of troubleshooting a cluster that is still running, but is taking a long time to return results\. For more information about what to do if the cluster has terminated with an error code, see [Troubleshoot a Failed Cluster](emr-troubleshoot-failed.md) 

 Amazon EMR enables you to specify the number and kind of instances in the cluster\. These specifications are the primary means of affecting the speed with which your data processing completes\. One thing you might consider is re\-running the cluster, this time specifying EC2 instances with greater resources, or specifying a larger number of instances in the cluster\. For more information, see [Configure Cluster Hardware and Networking](emr-plan-instances.md)\. 

 The following topics walk you through the process of identifying alternative causes of a slow cluster\. 

**Topics**
+ [Step 1: Gather Data About the Issue](emr-troubleshoot-slow-1.md)
+ [Step 2: Check the Environment](emr-troubleshoot-slow-2.md)
+ [Step 3: Examine the Log Files](emr-troubleshoot-slow-3.md)
+ [Step 4: Check Cluster and Instance Health](emr-troubleshoot-slow-4.md)
+ [Step 5: Check for Arrested Groups](emr-troubleshoot-slow-5.md)
+ [Step 6: Review Configuration Settings](emr-troubleshoot-slow-6.md)
+ [Step 7: Examine Input Data](emr-troubleshoot-slow-7.md)