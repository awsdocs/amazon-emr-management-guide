# Configure Additional Security Groups<a name="emr-additional-sec-groups"></a>

Whether you use the default managed security groups or your own custom managed security groups, you can assign additional security groups to the master and core/task instances in your cluster\. Applying additional security groups gives you the ability to apply additional rules to your security groups so they do not have to be modified\. Additional security groups are optional\. They can be applied to the master group, core/task group, both groups, or neither group\. You can also apply the same additional security groups to multiple clusters\.

For example, if you are using your own managed security groups, and you want to allow inbound SSH access to the master group for a particular cluster, you can create an additional security group containing the rule and add it to the master security group for that cluster\. Additional security groups are not modified by or maintained by Amazon EMR\. 

Typically, additional security groups are used to: 
+ Add access rules to instances in your cluster that are not present in the Amazon EMR–managed security groups
+ Give particular clusters access to a specific resource such as an Amazon Redshift database

Security groups are restrictive by default\. They reject all traffic\. You can add a rule to allow traffic on a particular port to your custom or additional security groups\. If there is more than one rule for a specific port in two security groups that apply to the same instances, the most permissive rule is applied\. For example, if you have a rule that allows SSH access via TCP port 22 from IP address 203\.0\.113\.1 and another rule that allows access to TCP port 22 from any IP address \(0\.0\.0\.0/0\), the rule allowing access by any IP address takes precedence\. 

You can apply up to four additional security groups to both the master and core/task security groups\. The number of additional groups allowed is dependent upon:
+ The number of security groups allowed for your account
+ The number of individual rules allowed for your account

For more information about rule limits in VPC security groups, see [Security Groups for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\. You can assign the same additional security groups to both the master and core/task security groups\.

Additional security groups can be applied using the console, the API, the CLI, or the SDK\.

**To specify additional security groups using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. In the **Security and Access** section, in the **EC2 Security Groups** subsection:
   + For **Master**, choose either the default security group or a custom security group from the list\. 
   + In the **Additional security groups** column, choose the icon to add up to four additional groups to the master security group\.
   + For **Core & Task**, choose either the default security group or a custom security group from the list\. 
   + In the **Additional security groups** column, choose the icon to add up to four additional groups to the core/task security group\.
**Note**  
You cannot combine custom and default security groups\.

1. Proceed with creating the cluster as described in [Plan and Configure Clusters](emr-plan.md)\.

**To specify additional security groups using the AWS CLI**

Use the `create-cluster` command with the `--ec2-attributes` parameter, specifying security group IDs for the `AdditionalSlaveSecurityGroups` and `AdditionalMasterSecurityGroups` variables\. Additional security groups are optional\. They can be applied to the master group, core/task group, both groups, or neither group\.
**Note**  
Amazon EMR\-managed security groups and additional security groups are not supported in the Amazon EMR CLI\.

1. To launch a cluster using additional security groups, type the following command\. Replace *myKey* with the name of your Amazon EC2 key pair, and replace *securityGroupId* with the ID of your master security group, core/task security group, and additional security groups\.

   ```
   1. aws emr create-cluster --name "Test cluster" --release-label emr-4.2.0 --applications Name=Hue Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey,ServiceAccessSecurityGroup=sg-service-accessId,EmrManagedMasterSecurityGroup=sg-masterId,EmrManagedSlaveSecurityGroup=sg-slaveId,AdditionalMasterSecurityGroups=securityGroupId,AdditionalSlaveSecurityGroups=securityGroupId --instance-type m3.xlarge --instance-count 3
   ```

   When you specify the instance count without using the `--instance-groups` parameter, a single master node is launched, and the remaining instances are launched as core nodes\. All nodes use the instance type specified in the command\.

1. To retrieve security group information for your cluster, type the following command and replace *j\-1K48XXXXXXHCB* with your cluster ID:

   ```
   aws emr describe-cluster --cluster-id j-1K48XXXXXXHCB
   ```

   For more information, see [Amazon EMR commands in the AWS CLI](http://docs.aws.amazon.com/cli/latest/reference/emr/index.html)\.