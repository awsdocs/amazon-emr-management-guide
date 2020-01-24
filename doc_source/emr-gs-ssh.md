# Step 3: Allow SSH Connections to the Cluster From Your Client<a name="emr-gs-ssh"></a>

Security groups act as virtual firewalls to control inbound and outbound traffic to your cluster\. When you create your first cluster, Amazon EMR creates the default Amazon EMR\-managed security group associated with the master instance, **ElasticMapReduce\-master**, and the security group associated with core and task nodes, **ElasticMapReduce\-slave**\.

**Warning**  
The default EMR\-managed security group for the master instance in public subnets, `ElasticMapReduce-master`, is pre\-configured with a rule that allows inbound traffic on Port 22 from all sources \(IPv4 0\.0\.0\.0/0\)\. This is to simplify initial SSH client connections to the master node\. We strongly recommend that you edit this inbound rule to restrict traffic only from trusted sources or specify a custom security group that restricts access\.

Modifying security groups isn't a requirement to complete the tutorial, but we recommend that you do not allow inbound traffic from all sources\. In addition, if another user edited the ElasticMapReduce\-master security group to eliminate this rule per the recommendations, you are not able to access the cluster using SSH for next steps\. For more information about security groups, see [Control Network Traffic with Security Groups](emr-security-groups.md) and [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups) in the *Amazon VPC User Guide*\.

**To remove the inbound rule that allows public access using SSH for the ElasticMapReduce\-master security group**

The following procedure assumes that the **ElasticMapReduce\-master** security group has not been edited previously\. In addition, to edit security groups, you must be logged in to AWS as a root user or as an IAM principal that is allowed to manage security groups for the VPC that the cluster is in\. For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) and the [Example Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html) that allows managing EC2 security groups in the *IAM User Guide*\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**\.

1. Choose the **Name** of the cluster\.

1. Under **Security and access** choose the **Security groups for Master** link\.  
![\[Edit security groups from EMR cluster status.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-sg-select.png)

1. Choose **ElasticMapReduce\-master** from the list\.

1. Choose **Inbound**, **Edit**\.

1. Find the rule with the following settings and choose the **x** icon to delete it:
   + **Type**

     SSH
   + **Port**

     22
   + **Source**

     Custom 0\.0\.0\.0/0

1. Scroll to the bottom of the list of rules and choose **Add Rule**\.

1. For **Type**, select **SSH**\.

   This automatically enters **TCP** for **Protocol** and **22** for **Port Range**\.

1. For source, select **My IP**\.

   This automatically adds the IP address of your client computer as the source address\. Alternatively, you can add a range of **Custom** trusted client IP addresses and choose **Add rule** to create additional rules for other clients\. In many network environments, IP addresses are allocated dynamically, so you may need to periodically edit security group rules to update the IP address of trusted clients\.

1. Choose **Save**\.

1. Optionally, choose **ElasticMapReduce\-slave** from the list and repeat the steps above to allow SSH client access to core and task nodes from trusted clients\.