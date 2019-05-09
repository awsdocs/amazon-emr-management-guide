# Control Network Traffic with Security Groups<a name="emr-security-groups"></a>

Security groups act as virtual firewalls for EC2 instances in your cluster to control inbound and outbound traffic\. Each security group has a set of rules that control inbound traffic, and a separate set of rules to control outbound traffic\. For more information, see [Amazon EC2 Security Groups for Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html)\.

You use two classes of security groups with Amazon EMR: *Amazon EMR\-managed security groups* and *additional security groups*\.

Every cluster has managed security groups associated with it\. You can use the default managed security groups, or specify custom managed security groups\. Either way, Amazon EMR automatically adds rules to managed security groups that a cluster needs to communicate between cluster instances and AWS services\.

Additional security groups are optional\. You can specify them in addition to managed security groups to tailor access to cluster instances\. Additional security groups contain only rules that you define\. Amazon EMR does not modify them\.

The rules that Amazon EMR creates in managed security groups only allow the cluster to communicate among internal components\. To allow users and applications to access a cluster from outside the cluster, you can edit rules in managed security groups, you can create additional security groups with additional rules, or do both\.

For example, to allow a trusted client to connect to the master node using SSH, you must create an inbound rule associated with the master instance that allows SSH \(Port 22\) from the client\. You can add this rule to the managed security group for the master instance after the cluster is created, or you can add it to an additional security group that you specify when you create the cluster\.

You can specify security groups only when you create a cluster\. They can't be added to a cluster or cluster instances while a cluster is running, but you can edit, add, and remove rules from existing security groups\. The rules take effect as soon as you save them\.

Security groups are restrictive by default\. Unless a rule is added that allows traffic, the traffic is rejected\. If there is more than one rule that applies to the same traffic and the same source, the most permissive rule applies\. For example, if you have a rule that allows SSH from IP address 192\.0\.2\.12/32, and another rule that allows access to all TCP traffic from the range 192\.0\.2\.0/24, the rule that allows all TCP traffic from the range that includes 192\.0\.2\.12 takes precedence\. In this case, the client at 192\.0\.2\.12 might have more access than you intended\. 

**Important**  
Use caution when you edit security group rules\. Be sure to add rules that only allow traffic from trusted clients for the protocols and ports that are required\. We do not recommend any inbound rules that allow traffic from 0\.0\.0\.0/0, which opens your cluster to that traffic from any client on the internet\.  
In addition, editing rules in managed security groups may have unintended consequences\. You may inadvertently block the traffic required for clusters to function properly and cause errors because nodes are unreachable\. Carefully plan and test security group configurations before implementation\.

**Topics**
+ [Working With Amazon EMR\-Managed Security Groups](emr-man-sec-groups.md)
+ [Working With Additional Security Groups](emr-additional-sec-groups.md)
+ [Specifying Amazon EMR\-Managed and Additional Security Groups](emr-sg-specify.md)