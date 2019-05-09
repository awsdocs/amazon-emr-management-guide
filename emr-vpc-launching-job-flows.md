# Launch Clusters into a VPC<a name="emr-vpc-launching-job-flows"></a>

After you have a subnet that is configured to host Amazon EMR clusters, launch the cluster in that subnet by specifying the associated subnet identifier when creating the cluster\.

**Note**  
Amazon EMR supports private subnets in release versions 4\.2 and above\. 

When the cluster is launched, Amazon EMR adds security groups based on whether the cluster is launching into VPC private or public subnets\. All security groups allow ingress at port 8443 to communicate to the Amazon EMR service, but IP address ranges vary for public and private subnets\. Amazon EMR manages all of these security groups, and may need to add additional IP addresses to the AWS range over time\. For more information, see [Control Network Traffic with Security Groups](emr-security-groups.md)\.

To manage the cluster on a VPC, Amazon EMR attaches a network device to the master node and manages it through this device\. You can view this device using the Amazon EC2 API action [https://docs.aws.amazon.com/AWSEC2/latest/APIReference//ApiReference-query-DescribeInstances.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference//ApiReference-query-DescribeInstances.html)\. If you modify this device in any way, the cluster may fail\.

**To launch a cluster into a VPC using the Amazon EMR console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. In the **Hardware Configuration** section, for **Network**, select the ID of a VPC network that you created previously\.

1. For **EC2 Subnet**, select the ID of a subnet that you created previously\.

   1. If your private subnet is properly configured with NAT instance and S3 endpoint options, it displays **\(EMR Ready\)** above the subnet names and identifiers\.

   1. If your private subnet does not have a NAT instance and/or S3 endpoint, you can configure this by choosing **Add S3 endpoint and NAT instance**, **Add S3 endpoint**, or **Add NAT instance**\. Select the desired options for your NAT instance and S3 endpoint and choose **Configure**\.
**Important**  
In order to create a NAT instance from the Amazon EMR, you need ec2:CreateRoute, `ec2:RevokeSecurityGroupEgress`, `ec2:AuthorizeSecurityGroupEgress`, `cloudformation:DescribeStackEvents` and `cloudformation:CreateStack` permissions\.
**Note**  
There is an additional cost for launching an EC2 instance for your NAT device\.

1. Proceed with creating the cluster\.

**To launch a cluster into a VPC using the AWS CLI**
**Note**  
The AWS CLI does not provide a way to create a NAT instance automatically and connect it to your private subnet\. However, to create a S3 endpoint in your subnet, you can use the Amazon VPCCLI commands\. Use the console to create NAT instances and launch clusters in a private subnet\.

After your VPC is configured, you can launch EMR clusters in it by using the `create-cluster` subcommand with the `--ec2-attributes` parameter\. Use the `--ec2-attributes` parameter to specify the VPC subnet for your cluster\.
+ To create a cluster in a specific subnet, type the following command, replace *myKey* with the name of your EC2 key pair, and replace *77XXXX03* with your subnet ID\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-4.2.0 --applications Name=Hadoop Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey,SubnetId=subnet-77XXXX03 --instance-type m4.large --instance-count 3
  ```

  When you specify the instance count without using the `--instance-groups` parameter, a single master node is launched, and the remaining instances are launched as core nodes\. All nodes use the instance type specified in the command\.
**Note**  
If you have not previously created the default Amazon EMR service role and EC2 instance profile, type `aws emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

For more information about using Amazon EMR commands in the AWS CLI, see the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/emr)\.