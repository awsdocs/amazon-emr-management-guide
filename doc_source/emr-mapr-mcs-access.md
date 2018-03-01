# Enabling MCS access for your Amazon EMR Cluster<a name="emr-mapr-mcs-access"></a>

After your MapR cluster is running, you need to open port 8453 to enable access to the MapR Control System \(MCS\) from hosts other than the host that launched the cluster\. Follow these steps to open the port\. 

**To open a port for MCS access**

1. Select your job from the list of jobs displayed in **Your Elastic MapReduce Job Flows** in the **Elastic MapReduce** tab of the AWS Management Console, then select the **Description** tab in the lower pane\. Make a note of the **Master Public DNS Name** value\. 

1. Click the **Amazon EC2** tab in the AWS Management Console to open the Amazon EC2 console\.

1. In the navigation pane, select **Security Groups** under the **Network and Security** group\.

1. In the **Security Groups** list, select **Elastic MapReduce\-master**\.

1. In the lower pane, click the **Inbound** tab\.

1. In **Port Range:**, type 8453\. Leave the default value in the **Source:** field\.
**Note**  
The standard MapR port is 8443\. Use port number 8453 instead of 8443 when you use the MapR REST API calls to MapR on an Amazon EMR cluster\.

1. Click **Add Rule**, then click **Apply Rule Changes**\.

1. You can now navigate to the master node's DNS address\. Connect to port 8453 to log in to the MapR Control System\. Use the string **hadoop** for both login and password at the MCS login screen\.

**Note**  
For M5 MapR clusters on Amazon EMR, the MCS web server runs on the primary and secondary CLDB nodes, giving you another entry point to the MCS if the primary fails\.