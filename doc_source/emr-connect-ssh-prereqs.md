# Before you connect: Authorize inbound traffic<a name="emr-connect-ssh-prereqs"></a>

Before you connect to an Amazon EMR cluster, you must authorize inbound SSH traffic \(port 22\) from trusted clients such as your computer's IP address\. In order to do so, edit the managed security group rules for the nodes to which you want to connect\. For example, the following instructions show you how to add an inbound rule for SSH access to the default ElasticMapReduce\-master security group\.

For more information about using security groups with Amazon EMR, see [Control network traffic with security groups](emr-security-groups.md)\.

**To allow SSH access for trusted sources for the ElasticMapReduce\-master security group**

To edit your security groups, you must have permission to manage security groups for the VPC that the cluster is in\. For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) and the [Example Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html) that allows managing EC2 security groups in the *IAM User Guide*\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**\.

1. Choose the **Name** of the cluster you want to modify\.

1. Choose the **Security groups for Master** link under **Security and access**\.

1. Choose **ElasticMapReduce\-master** from the list\.

1. Choose the **Inbound rules** tab and then **Edit inbound rules**\.

1. Check for an inbound rule that allows public access with the following settings\. If it exists, choose **Delete** to remove it\.
   + **Type**

     SSH
   + **Port**

     22
   + **Source**

     Custom 0\.0\.0\.0/0
**Warning**  
Before December 2020, the ElasticMapReduce\-master security group had a pre\-configured rule to allow inbound traffic on Port 22 from all sources\. This rule was created to simplify initial SSH connections to the master node\. We strongly recommend that you remove this inbound rule and restrict traffic to trusted sources\.

1. Scroll to the bottom of the list of rules and choose **Add Rule**\.

1. For **Type**, select **SSH**\.

   Selecting SSH automatically enters **TCP** for **Protocol** and **22** for **Port Range**\.

1. For source, select **My IP** to automatically add your IP address as the source address\. You can also add a range of **Custom** trusted client IP addresses, or create additional rules for other clients\. Many network environments dynamically allocate IP addresses, so you might need to update your IP addresses for trusted clients in the future\.

1. Choose **Save**\.

1. Optionally, choose **ElasticMapReduce\-slave** from the list and repeat the steps above to allow SSH client access to core and task nodes\.