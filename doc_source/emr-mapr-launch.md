# Launch an Amazon EMR cluster with MapR using the console<a name="emr-mapr-launch"></a>

You can launch any standard cluster on a MapR distribution by specifying MapR when you set the Hadoop version\. 

**To launch an Amazon EMR cluster with MapR using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. In the **Software Configuration** section, verify the fields according to the following table\.
**Important**  
If you launch a MapR job flow in a VPC, you must set `enableDnsHostnames` to true and all hosts in the cluster must have a fully qualified domain name\. For more information, see [Using DNS with Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide//vpc-dns.html) and [DHCP Options Sets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide//VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-mapr-launch.html)

1. Click **Done** and proceed with creating the cluster as described in [Plan and Configure Clusters](emr-plan.md)\.