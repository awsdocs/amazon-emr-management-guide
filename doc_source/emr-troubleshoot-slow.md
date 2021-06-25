# Troubleshoot a slow cluster<a name="emr-troubleshoot-slow"></a>

 This section walks you through the process of troubleshooting a cluster that is still running, but is taking a long time to return results\. For more information about what to do if the cluster has terminated with an error code, see [Troubleshoot a failed cluster](emr-troubleshoot-failed.md) 

 Amazon EMR enables you to specify the number and kind of instances in the cluster\. These specifications are the primary means of affecting the speed with which your data processing completes\. One thing you might consider is re\-running the cluster, this time specifying EC2 instances with greater resources, or specifying a larger number of instances in the cluster\. For more information, see [Configure cluster hardware and networking](emr-plan-instances.md)\. 

 The following topics walk you through the process of identifying alternative causes of a slow cluster\. 

**Topics**
+ [Step 1: Gather data about the issue](emr-troubleshoot-slow-1.md)
+ [Step 2: Check the environment](emr-troubleshoot-slow-2.md)
+ [Step 3: Examine the log files](emr-troubleshoot-slow-3.md)
+ [Step 4: Check cluster and instance health](emr-troubleshoot-slow-4.md)
+ [Step 5: Check for suspended groups](emr-troubleshoot-slow-5.md)
+ [Step 6: Review configuration settings](emr-troubleshoot-slow-6.md)
+ [Step 7: Examine input data](emr-troubleshoot-slow-7.md)