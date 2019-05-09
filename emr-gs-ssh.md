# Step 3: Allow SSH Connections to the Cluster From Your Client<a name="emr-gs-ssh"></a>

Security groups act as virtual firewalls to control inbound and outbound traffic to your cluster\. The default Amazon EMR\-managed security groups associated with cluster instances do not allow inbound SSH connections as a security precaution\. To connect to cluster nodes using SSH so that you can use the command line and view web interfaces that are hosted on the cluster, you need to add inbound rules that allow SSH traffic from trusted clients\. Allowing SSH from your client isn't a requirement to complete the tutorial—the Hive script runs and you can access the results in Amazon S3—but accessing clusters using SSH is a common requirement\. Performing this step allows you to explore next steps with your new cluster after you complete the tutorial\. For more information about security groups, see [Control Network Traffic with Security Groups](emr-security-groups.md) and [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups) in the *Amazon VPC User Guide*\.

**To allow inbound SSH traffic to cluster EC2 instances**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**\.

1. Choose the **Name** of the cluster\.

1. Under **Security and access** choose the **Security groups for Master** link\.  
![\[Edit security groups from EMR cluster status.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/../shared/images/cluster-sg-select.png)

1. Choose **ElasticMapReduce\-master** from the list\.

1. Choose **Inbound**, **Edit** to add a new inbound rule\.

1. Scroll down through the listing of default rules and choose **Add Rule** at the bottom of the list\.

1. For **Type**, select **SSH**\. For source, select **My IP**\.

   This automatically adds the IP address of your client computer as the source address\. Alternatively, you can add a range of **Custom** trusted client IP addresses and choose **Add rule** to create additional rules for other clients\. In many network environments, IP addresses are allocated dynamically, so you may need to periodically edit security group rules to update the IP address of trusted clients\.

1. Choose **Save**\.

1. Choose **ElasticMapReduce\-slave** from the list and repeat the steps above to allow SSH client access to core and task nodes from trusted clients\.