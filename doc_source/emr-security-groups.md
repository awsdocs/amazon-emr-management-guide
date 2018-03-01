# Control Network Traffic with Security Groups<a name="emr-security-groups"></a>

A security group acts as a virtual firewall for your EC2 instances to control inbound and outbound traffic\. For each security group, one set of rules controls the inbound traffic to instances, and a separate set of rules controls outbound traffic\. In Amazon EMR, there are two types of security groups for your EC2 instances:

+ Amazon EMR–managed security groups for the master and core/task instances: you can choose the default master and core/task security groups \(ElasticMapReduce\-master, ElasticMapReduce\-slave, ElasticMapReduce\-Master\-Private, ElasticMapReduce\-Slave\-Private, and ElasticMapReduce\-ServiceAccess\), or you can specify your own security groups for the master and core/task instances

+ Additional security groups: you can specify security groups that apply to your Amazon EC2 instances in addition to the Amazon EMR–managed security groups

Security groups need to be specified at cluster launch using the console, CLI, API, or SDK\. Instances in running clusters cannot be assigned new security groups, but you can edit or add rules to existing security groups\. Edited rules and new rules added to security groups take effect when the rules are saved\.

For more information, see [Amazon EC2 Security Groups](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about Amazon VPC security groups, see [Security Groups for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\.


+ [Use Amazon EMR–Managed Security Groups](emr-man-sec-groups.md)
+ [Configure Additional Security Groups](emr-additional-sec-groups.md)