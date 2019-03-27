# Configure EC2 Instances<a name="emr-plan-ec2-instances"></a>

EC2 instances come in different configurations, which are known as *instance types*\. Each instance type has different CPU, input/output, and storage capacities\. In addition to the instance type, you can choose different purchasing options for EC2 instances\. You can specify different instance types and purchasing options within instance groups or instance fleets\. For more information, see [Create a Cluster with Instance Fleets or Uniform Instance Groups](emr-instance-group-configuration.md)\. For guidance about choosing the right options, see [Cluster Configuration Guidelines and Best Practices](emr-plan-instances-guidelines.md)\.

**Important**  
When you choose an instance type using the AWS Management Console, the number of **vCPU** shown for each **Instance type** is the number of YARN vcores for that instance type, not the number of EC2 vCPUs for that instance type\. For more information on the number of vCPUs for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

**Topics**
+ [Supported Instance Types](emr-supported-instance-types.md)
+ [Instance Purchasing Options](emr-instance-purchasing-options.md)
+ [Instance Storage](emr-plan-storage.md)