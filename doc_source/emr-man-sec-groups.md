# Use Amazon EMR–Managed Security Groups<a name="emr-man-sec-groups"></a>

When you launch an Amazon EMR cluster, you must specify one Amazon EMR–managed security group for the master instance, one for Amazon EMR–managed security group for the core/task \(slave\) instances, and optionally, one for the Amazon EMR resources used to manage clusters in private subnets\. The core/task instances share the same security group\. 

You can use the default Amazon EMR–managed security groups for cluster resources or you can choose your own security groups for the master and core/task instances\. You cannot combine your own security groups with the default groups\.

When you launch a cluster using the console, if the default security groups do not exist, the Amazon EMR–managed security groups fields are populated with **Create ElasticMapReduce\-master** and **Create ElasticMapReduce\-slave** or **Create ElasticMapReduce\-Master\-Private**, **Create ElasticMapReduce\-Slave\-Private**, and **Create ElasticMapReduce\-ServiceAccess**\. If the default security groups exist, the fields are populated with the default security group IDs; for example, **Default: sg\-01XXXX6a \(ElasticMapReduce\-master\)** and **Default: sg\-07XXXX6c \(ElasticMapReduce\-slave\)**\. To use your own security groups, choose them from the lists\. For more information about the default Amazon EMR–managed security group options, see [Default Options for Amazon EMR–Managed Security Groups](#emr-def-sec-groups)\.

Amazon EMR\-specific inbound and outbound access rules are written to and maintained in the Amazon EMR–managed security groups\. Whether you choose the default security groups or your own, Amazon EMR modifies the security group rules to ensure proper communication between instances in a cluster\. For more information about the rules written to the Amazon EMR–managed security groups, see [Rules in Amazon EMR–Managed Security Groups](#emr-man-sec-group-rules)\.

If you need to control instance membership in the Amazon EMR–managed security groups, you can create your own security groups for the master and core/task instances\. For more information, see [Security Groups for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\. When you create your own security groups, any inbound and outbound rules required for proper intra\-cluster communication are written to the groups with some exceptions\. 

Typically, you would create your own Amazon EMR–managed security groups to isolate clusters so they cannot communicate with each other\. This alleviates the need to create separate VPCs or AWS accounts for each of your clusters\. For example, to isolate Amazon EMR clusters in a VPC, you create a security group for each cluster's master node, and you create a security group for each cluster's core/task nodes\. When you launch a cluster, the rules in your security groups are applied only to the instances in that cluster, effectively preventing instances in separate clusters from communicating with each other\. 

If you need separate clusters to communicate, and you are using your own security groups, you can create an additional security group containing the rules necessary for the cluster instances to communicate with each other, or you can use the same security groups for both clusters\. If you do not specify an additional security group when launching the clusters, and the clusters use different security groups, the clusters cannot communicate with each other unless you modify the rule sets in your master and core/task security groups\. For more information about additional security groups, see [Configure Additional Security Groups ](emr-additional-sec-groups.md)\.

**To specify Amazon EMR–managed security groups using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. In the **Security and Access** section, in the **EC2 Security Groups** subsection:
   + For **Master**, if the default security groups do not exist, the field is populated with **Create ElasticMapReduce\-master** or **Create ElasticMapReduce\-Master\-Private**\. If the default security groups exist, the field is populated with the default security group ID; for example, **Default: sg\-01XXXX6a \(ElasticMapReduce\-master\)**\. To specify your own master security group, choose it from the list\.
   + For **Core & Task**, if the default security groups do not exist, the field is populated with **Create ElasticMapReduce\-slave** or **Create ElasticMapReduce\-Slave\-Private**\. If the default security groups exist, the field is populated with the default security group ID; for example, **Default: sg\-07XXXX6c \(ElasticMapReduce\-slave\)**\. To specify your own core/task security group, choose it from the list\.
   + \(Optional\) For **Service Access** to clusters in private subnets, if the default security groups do not exist, the field is populated with **Create ElasticMapReduce\-ServiceAccess**\. If the default security groups exist, the field is populated with the default security group ID; for example, **Default: sg\-4dXXXX34 \(ElasticMapReduce\-ServiceAccess\)**\. To specify your own service access security group, choose it from the list\.

1. Proceed with creating the cluster as described in [Use an Amazon EC2 Key Pair for SSH Credentials](emr-plan-access-ssh.md)\.

**To specify Amazon EMR–managed security groups using the AWS CLI**

Use the `create-cluster` command with the `--emr-managed-master-security-group` and `--emr-managed-slave-security-group` parameters\. 

If you are using the default options for Amazon EMR–managed security groups, no additional parameters are required\. Use the `create-cluster` command as you normally would, using the CLI\. If the default security groups do not exist, they are created before your cluster is launched\. If they do exist, they are automatically assigned\.
**Note**  
Amazon EMR–managed security groups are not supported in the Amazon EMR CLI\.

1. To launch a cluster using the default security group options, type the following command, replace *myKey* with the name of your Amazon EC2 key pair\.

   ```
   aws emr create-cluster --name "Test cluster" --release-label emr-4.2.0 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-type m4.large --instance-count 3
   ```

   When you specify the instance count without using the `--instance-groups` parameter, a single master node is launched, and the remaining instances are launched as core nodes\. All nodes use the instance type specified in the command\.

1. To launch a cluster using your own security groups, type the following command, replace *myKey* with the name of your Amazon EC2 key pair, replace *masterSecurityGroupId* with the ID of the master security group, and replace *slaveSecurityGroupId* with the ID of the core/task security group\.

   ```
   aws emr create-cluster --name "Test cluster" --release-label emr-4.2.0 --applications Name=Hive Name=Pig --ec2-attributes KeyName=myKey,EmrManagedMasterSecurityGroup=sg-masterId,EmrManagedSlaveSecurityGroup=sg-slaveId --instance-type m4.large --instance-count 3 --use-default-roles
   ```
**Note**  
For private subnets, you can additionally specify `ServiceAccessSecurityGroup=sg-service-accessId` with the `--ec2-attributes` parameter\.

   When you specify the instance count without using the `--instance-groups` parameter, a single master node is launched, and the remaining instances are launched as core nodes\. All nodes use the instance type specified in the command\.

1. To retrieve security group information for your cluster, type the following command and replace *j\-1K48XXXXXXHCB* with your cluster ID:

   ```
   aws emr describe-cluster --cluster-id j-1K48XXXXXXHCB
   ```

   For more information, see [Amazon EMR commands in the AWS CLI](http://docs.aws.amazon.com/cli/latest/reference/emr/index.html)\.

## Default Options for Amazon EMR–Managed Security Groups<a name="emr-def-sec-groups"></a>

If you launch an Amazon EMR cluster using the default security groups, two groups are created for public subnets: ElasticMapReduce\-master and ElasticMapReduce\-slave\. For private subnets, three groups are created:
+ Create `ElasticMapReduce-Master-Private`
+ Create `ElasticMapReduce-Slave-Private`
+ Create `ElasticMapReduce-ServiceAccess`

The inbound and outbound access rules written to these groups ensure that the master and core/task instances in a cluster can communicate properly\. 

In addition, if you launch other Amazon EMR clusters in the same VPC using the default security groups, the instances in those clusters can communicate with the instances in any other Amazon EMR cluster within that VPC whose instances also belong to the same security groups\. 

You can launch a cluster with the default security groups using the console, the API, the CLI, or the SDK\. If you use the default security groups, there is no need to change your existing code or to add parameters to your CLI commands\.

You can launch a cluster with the default security groups using the console\. If the default security groups do not exist, they are created before your cluster is launched\. If they do exist, they are automatically assigned\.

## Rules in Amazon EMR–Managed Security Groups<a name="emr-man-sec-group-rules"></a>

The tables in this section list the inbound and outbound access rules added to the Amazon EMR–managed security groups\. IP address ranges and rules are automatically updated for Amazon EMR–managed security groups\.

**Warning**  
We do not recommend creating an inbound rule for any protocol or port that allows inbound traffic from all IP addresses \(0\.0\.0\.0/0\)\. This opens access to everyone and creates a security vulnerability\.

The following rules are added to Amazon EMR–managed master security groups:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)

The following rules are added to Amazon EMR–managed core/task security groups:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-man-sec-groups.html)