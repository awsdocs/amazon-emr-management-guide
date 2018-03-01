# Access the Web Interfaces on the Master Node Using the Console<a name="emr-connect-ui-console"></a>

If you already have an SSH tunnel configured with the Amazon EMR master node using dynamic port forwarding, you can open the web interfaces using the console\.<a name="tunnel-console"></a>

**To open the web interfaces using the console**

1. Verify that you have established an SSH tunnel with the master node and that you have a proxy management add\-on configured for your browser\. 

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Cluster List** page, choose the link for your cluster\.

1. In the cluster details, for **Connections**, choose the link for the web interface you wish to open in your browser\.   
![\[Open the web interfaces\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/console-connect-tunnel-on.png)

1. Alternatively, choose the **View All** link to display links to all of the available web interfaces on your cluster's master node\. Choosing the links opens the interfaces in your browser\.  
![\[Web interface links\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/console-connect-tunnel-on-interfaces.png)

   If you do not have an SSH tunnel open with the master node, choose **Enable Web Connection** for instructions on creating a tunnel\.   
![\[Enable web connection link\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/console-connect-tunnel-off.png)
**Note**  
If you have an SSH tunnel configured using local port forwarding, the Amazon EMR console does not detect the connection\.