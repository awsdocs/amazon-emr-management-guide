# Updated Console Options for Cluster Scaling<a name="managed-scaling-console-updates"></a>

The cluster scaling options in the console have changed\. The options are consolidated into one **Cluster Scaling** section on the **Hardware Configuration** page in **Create Cluster \- Advanced Options**\. The cluster scaling options can also be edited on the **Hardware tab** of a running cluster\. 

You can select **Use EMR\-managed scaling** or **Create a custom automatic scaling policy** when you enable cluster scaling\. **Use EMR\-managed scaling** is only available with Amazon EMR version 5\.30\.0 and later\. For more information, see [Scaling Cluster Resources](emr-scale-on-demand.md)\.

The following screenshots show the differences between the previous automatic scaling and the current cluster scaling options in the console\. 

**Changes on the **Hardware Configuration** page**

When you use the advanced option to create a cluster, the options for automatic scaling are moved from the instance group table to the **Cluster scaling** section on the **Hardware Configuration** page\. 

![\[Console options for cluster scaling in advanced option\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/Cluster_Scaling_Advanced_Comparison.png)

**Changes on the Hardware tab**

In the **Hardware** tab of a running cluster, the options for automatic scaling are moved to the **Cluster scaling** table on the same page\. 

![\[Console options for cluster scaling in the Hardware tab\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/Cluster_Scaling_HardwareTab_Comparison.png)