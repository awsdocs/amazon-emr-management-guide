# Configure EC2 instances<a name="emr-plan-ec2-instances"></a>

EC2 instances come in different configurations known as *instance types*\. Instance types have different CPU, input/output, and storage capacities\. In addition to the instance type, you can choose different purchasing options for EC2 instances\. You can specify different instance types and purchasing options within uniform instance groups or instance fleets\. For more information, see [Create a cluster with instance fleets or uniform instance groups](emr-instance-group-configuration.md)\. For guidance about choosing instance types and purchasing options for your application, see [Cluster configuration guidelines and best practices](emr-plan-instances-guidelines.md)\.

**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

**Topics**
+ [Supported instance types](emr-supported-instance-types.md)
+ [Instance purchasing options](emr-instance-purchasing-options.md)
+ [Instance storage](emr-plan-storage.md)