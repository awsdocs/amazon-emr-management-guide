# Cloning a Cluster Using the Console<a name="clone-console"></a>

You can use the Amazon EMR console to clone a cluster, which makes a copy of the configuration of the original cluster to use as the basis for a new cluster\. 

**To clone a cluster using the console**

1. From the **Cluster List** page, click a cluster to clone\.

1. At the top of the **Cluster Details** page, click **Clone**\. 

   In the dialog box, choose **Yes** to include the steps from the original cluster in the cloned cluster\. Choose **No** to clone the original cluster's configuration without including any of the steps\.
**Note**  
For clusters created using AMI 3\.1\.1 and later \(Hadoop 2\.x\) or AMI 2\.4\.8 and later \(Hadoop 1\.x\), if you clone a cluster and include steps, all system steps \(such as configuring Hive\) are cloned along with user\-submitted steps, up to 1,000 total\. Any older steps that no longer appear in the console's step history cannot be cloned\. For earlier AMIs, only 256 steps can be cloned \(including system steps\)\. For more information, see [Submit Work to a Cluster](AddingStepstoaJobFlow.md)\.

1. The **Create Cluster** page appears with a copy of the original cluster's configuration\. Review the configuration, make any necessary changes, and then click **Create Cluster**\.