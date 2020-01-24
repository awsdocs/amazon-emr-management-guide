# EMR Clusters on AWS Outposts<a name="emr-plan-outposts"></a>

Beginning with Amazon EMR version 5\.28\.0, you can create and run EMR clusters on AWS Outposts\. AWS Outposts enables native AWS services, infrastructure, and operating models in on\-premises facilities\. In AWS Outposts environments, you can use the same AWS APIs, tools, and infrastructure that you use in the AWS Cloud\. Amazon EMR on AWS Outposts is ideal for low latency workloads that need to be run in close proximity to on\-premises data and applications\. For more information about AWS Outposts, see [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/)\. 

## Prerequisites<a name="emr-outposts-prereq"></a>

 The following are the prerequisites for using Amazon EMR on AWS Outposts:
+ You must have installed and configured AWS Outposts in your on\-premises data center\.
+ You must have a reliable network connection between your Outpost environment and an AWS Region\.
+ You must have sufficient capacity for EMR supported instance types available in your Outpost\.

## Limitations<a name="emr-outposts-limit"></a>

The following are the limitations of using Amazon EMR on AWS Outposts:
+ On\-Demand Instances are the only supported option for Amazon EC2 instances\. Spot Instances are not available for Amazon EMR on AWS Outposts\.
+ If you need additional Amazon EBS storage volumes, only General Purpose SSD \(GP2\) is supported\. 
+ Only the following instance types are supported by Amazon EMR on AWS Outposts:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-outposts.html)

## Network Connectivity Considerations<a name="emr-outposts-considerations"></a>
+ If network connectivity between your Outpost and its AWS Region is lost, your clusters will continue to run\. However, you cannot create new clusters or take new actions on existing clusters until connectivity is restored\. In case of instance failures, the instance will not be automatically replaced\. Additionally, actions such as adding steps to a running cluster, checking step execution status, and sending CloudWatch metrics and events will be delayed\. 
+ We recommend that you provide reliable and highly available network connectivity between your Outpost and the AWS Region\. If network connectivity between your Outpost and its AWS Region is lost for more than a few hours, clusters that have enabled terminate protection will continue to run, and clusters that have disabled terminate protection may be terminated\. 
+ If network connectivity will be impacted due to routine maintenance, we recommend proactively enabling terminate protection\. More generally, connectivity interruption means that any external dependencies that are not local to the Outpost or customer network will not be accessible\. This includes Amazon S3, DynamoDB used with EMRFS consistency view, and Amazon RDS if an in\-region instance is used for an EMR cluster with multiple master nodes\.

## Creating an Amazon EMR Cluster on AWS Outposts<a name="emr-outposts-create"></a>

Creating an Amazon EMR cluster on AWS Outposts is similar to creating an Amazon EMR cluster in the AWS Cloud\. When you create an Amazon EMR cluster on AWS Outposts, you must specify an Amazon EC2 subnet associated with your Outpost\.

An Amazon VPC can span all of the Availability Zones in an AWS Region\. AWS Outposts are extensions of Availability Zones, and you can extend an Amazon VPC in an account to span multiple Availability Zones and associated Outpost locations\. When you configure your Outpost, you associate a subnet with it to extend your Regional VPC environment to your on\-premises facility\. Outpost instances and related services appear as part of your Regional VPC, similar to an Availability Zone with associated subnets\. For information, see [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/)\.

**Console**

To create a new Amazon EMR cluster on AWS Outposts with the AWS Management Console, specify an Amazon EC2 subnet that is associated with your Outpost\.

1. Open the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. Under **Software Configuration**, for **Release**, choose 5\.28\.0 or later\.

1. Under **Hardware Configuration**, for **EC2 Subnet**, select an EC2 subnet with an Outpost ID in this format: op\-123456789\.

1. Choose instance type or add Amazon EBS storage volumes for uniform instance groups or instance fleets\. Limited Amazon EBS volume and instance types are supported for Amazon EMR on AWS Outposts\.

**AWS CLI**

 To create a new Amazon EMR cluster on AWS Outposts with the AWS CLI, specify an EC2 subnet that is associated with your Outpost\.

The following example creates an Amazon EMR cluster on an Outpost\. Replace *subnet\-22XXXX01* with an EC2 subnet that is associated with your Outpost\. 

```
aws emr create-cluster \
--name "Outpost cluster" \
--release-label emr-5.29.0 \
--applications Name=Spark \
--ec2-attributes KeyName=myKey SubnetId=subnet-22XXXX01 \
--instance-type m5.xlarge --instance-count 3 --use-default-roles
```