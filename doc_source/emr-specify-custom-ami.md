# Specifying a custom AMI<a name="emr-specify-custom-ami"></a>

You can specify a custom AMI ID when you create a cluster using the AWS Management Console, AWS CLI, [Amazon CloudWatch](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticmapreduce-cluster.html), or the Amazon EMR API\. The AMI must exist in the same AWS Region where you create the cluster\. The AWS Management Console doesn’t currently support creating a cluster using instance\-level custom AMIs\. To create a cluster using instance\-level custom AMIs, use the AWS CLI or [RunJobFlow](https://docs.aws.amazon.com/emr/latest/APIReference/API_RunJobFlow.html) in the *Amazon EMR API Reference*\.

To specify multiple custom AMIs and multiple instance architectures when configuring an Amazon EMR instance fleet or uniform instance group, see [Create a cluster with the instance fleets configuration](emr-instance-fleet.md#create-cluster-instance-fleet-cli) and [Use the AWS CLI to create a cluster with uniform instance groups](emr-uniform-instance-group.md#emr-uniform-instance-group-cli)\.<a name="emr-specify-custom-ami-console"></a>

**To specify a custom AMI using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Under **Software Configuration**, for **Release**, choose **emr\-5\.7\.0** or later and then choose other options as appropriate for your application\. Choose **Next**\.

1. Select values under **Hardware Configuration** that are appropriate for your application, and choose **Next**\.

1. Under **Additional Options**, for **Custom AMI ID**, enter a value and make sure that the **Update all installed packages on reboot** option is selected\. For more information about changing the update option, see [Managing AMI package repository updates](#emr-custom-ami-package-update)\.  
![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-custom-ami.png)

1. To launch the cluster, choose **Next** and complete other configuration options\.

**To specify a cluster\-level custom AMI using the AWS CLI**
+ Use the `--custom-ami-id` parameter to specify the AMI ID when you run the `aws emr [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html)` command\.

  The following example specifies a cluster that uses a cluster\-level custom AMI with a 20 GiB boot volume\. For more information, see [Specifying the Amazon EBS root device volume size](emr-custom-ami-boot-volume-size.md)\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Cluster with My Custom AMI" \
  --custom-ami-id MyAmiID --ebs-root-volume-size 20 \
  --release-label emr-5.7.0 --use-default-roles \
  --instance-count 2 --instance-type m5.xlarge
  ```

**Example \- Use the AWS CLI to create an instance group cluster using instance\-level custom AMIs**  
Using the AWS CLI, you can assign a single cluster\-level custom AMI to the entire cluster, or you can assign instance\-level custom AMIs to every instance node in your cluster\. You cannot launch a cluster with both a cluster\-level custom AMI and instance\-level custom AMIs\. For example, you can configure x86\_64 architecture \(m5\.xlarge\) and arm64 architecture \(m6g\.xlarge\) instances in the same instance fleet or instance group, with each instance type using a unique custom AMI that matches the instance type architecture\. Similarly, a m6g\.xlarge instance type, which has arm64 architecture, would need to be matched with a corresponding arm64 architecture custom AMI\.  
The following example shows a uniform instance group cluster created with two instance types \(m5\.xlarge\) used across node types \(master, core, task\)\. Each node has an instance\-level custom AMI\. The example illustrates several features of the instance\-level custom AMI configuration:   
+ There is no custom AMI assigned at the cluster level\. This is to avoid conflicts between the instance\-level custom AMIs and a cluster\-level custom AMI, which would cause the cluster launch to fail\.
+ The cluster can have multiple instance\-level custom AMIs across master, core, and individual task nodes\. This allows individual AMI customizations, such as pre\-installed applications, sophisticated cluster configurations, and encrypted Amazon EBS root device volumes\.
+ The instance group core node can have only one instance type and corresponding custom AMI\. Similarly, the master node can have only one instance type and corresponding custom AMI\. 
+ The cluster can have multiple task nodes\.

```
aws emr create-cluster --instance-groups 
InstanceGroupType=MASTER,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-123456 
InstanceGroupType=CORE,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-234567
InstanceGroupType=TASK,InstanceType=m6g.xlarge,InstanceCount=1,CustomAmiId=ami-345678
InstanceGroupType=TASK,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-456789
```

**Example \- Use the AWS CLI to add a task node to a running instance group cluster with multiple instance types and instance\-level custom AMIs**  
Using the AWS CLI, you can add instance\-level custom AMIs to an instance group that you add to a running cluster\. The CustomAmiId argument can be used with the add\-instance\-groups command as shown in the following example\. Notice that the same instance\-level custom AMI ID \(ami\-123456\) is used in more than one node\.   

```
aws emr create-cluster --instance-groups 
InstanceGroupType=MASTER,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-123456 
InstanceGroupType=CORE,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-123456
InstanceGroupType=TASK,InstanceType=m5.xlarge,InstanceCount=1,CustomAmiId=ami-234567

{
    "ClusterId": "j-123456",
    ...
}

aws emr add-instance-groups --cluster-id j-123456 --instance-groups InstanceGroupType=Task,InstanceType=m6g.xlarge,InstanceCount=1,CustomAmiId=ami-345678
```

**Example \- Use the AWS CLI to create an instance fleet cluster, instance\-level custom AMIs, multiple instance types, On\-Demand master, On\-Demand core, multiple core, and task nodes**  

```
aws emr create-cluster --instance-fleets 
InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge, CustomAmiId=ami-123456}'] 
InstanceFleetType=CORE,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-234567},{InstanceType=m6g.xlarge, CustomAmiId=ami-345678}']
InstanceFleetType=TASK,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-456789},{InstanceType=m6g.xlarge, CustomAmiId=ami-567890}']
```

**Example \- Use the AWS CLI to add task nodes to a running cluster with multiple instance types and instance\-level custom AMIs**  

```
aws emr create-cluster --instance-fleets 
InstanceFleetType=MASTER,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge, CustomAmiId=ami-123456}'] 
InstanceFleetType=CORE,TargetOnDemandCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-234567},{InstanceType=m6g.xlarge, CustomAmiId=ami-345678}']

{
    "ClusterId": "j-123456",
    ...
}

aws emr add-instance-fleet --cluster-id j-123456 --instance-fleet 
InstanceFleetType=TASK,TargetSpotCapacity=1,InstanceTypeConfigs=['{InstanceType=m5.xlarge,CustomAmiId=ami-234567},{InstanceType=m6g.xlarge, CustomAmiId=ami-345678}']
```

## Managing AMI package repository updates<a name="emr-custom-ami-package-update"></a>

On first boot, by default, Amazon Linux AMIs connect to package repositories to install security updates before other services start\. Depending on your requirements, you may choose to disable these updates when you specify a custom AMI for Amazon EMR\. The option to disable this feature is available only when you use a custom AMI\. By default, Amazon Linux kernel updates and other software packages that require a reboot are not updated\. Note that your networking configuration must allow for HTTP and HTTPS egress to Amazon Linux repositories in Amazon S3, otherwise security updates will not succeed\.

**Warning**  
We strongly recommend that you choose to update all installed packages on reboot when you specify a custom AMI\. Choosing not to update packages creates additional security risks\.

Using the AWS Management Console, you can select the option to disable updates when you choose **Custom AMI ID**\. 

Using the AWS CLI, you can specify `--repo-upgrade-on-boot NONE` along with `--custom-ami-id` when using the create\-cluster command\. 

Using the Amazon EMR API, you can specify `NONE` for the [RepoUpgradeOnBoot](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_RunJobFlow.html#EMR-RunJobFlow-request-RepoUpgradeOnBoot) parameter\.

## Creating a custom Amazon Linux AMI from a preconfigured instance<a name="emr-custom-ami-preconfigure"></a>

The basic steps for pre\-installing software and performing other configurations to create a custom Amazon Linux AMI for Amazon EMR are as follows:
+ Launch an instance from the base Amazon Linux AMI\.
+ Connect to the instance to install software and perform other customizations\.
+ Create a new image \(AMI snapshot\) of the instance you configured\.

After you create the image based on your customized instance, you can copy that image to an encrypted target as described in [Creating a custom AMI with an encrypted Amazon EBS root device volume](#emr-custom-ami-encrypted)\.

### Tutorial: Creating an AMI from an instance with custom software installed<a name="emr-custom-ami-preconfigure-walkthrough"></a>

**To launch an EC2 instance based on the most recent Amazon Linux AMI**

1. Use the AWS CLI to run the following command, which creates an instance from an existing AMI\. Replace `MyKeyName` with the key pair you use to connect to the instance and *MyAmiId* with the ID of an appropriate Amazon Linux AMI\. For the most recent AMI IDs, see [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   ```
   aws ec2 run-instances --image-id MyAmiID \
   --count 1 --instance-type m5.xlarge \
   --key-name MyKeyName --region us-west-2
   ```

   The `InstanceId` output value is used as `MyInstanceId` in the next step\.

1. Run the following command:

   ```
   aws ec2 describe-instances --instance-ids MyInstanceId
   ```

   The `PublicDnsName` output value is used to connect to the instance in the next step\.

**To connect to the instance and install software**

1. Use an SSH connection that lets you run shell commands on your Linux instance\. For more information, see [Connecting to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Perform any required customizations\. For example:

   ```
   sudo yum install MySoftwarePackage
   sudo pip install MySoftwarePackage
   ```

**To create a snapshot from your customized image**
+ After you customize the instance, use the `create-image` command to create an AMI from the instance\.

  ```
  aws ec2 create-image --no-dry-run --instance-id MyInstanceId --name MyEmrCustomAmi
  ```

  The `imageID` output value is used when you launch the cluster or create an encrypted snapshot\. For more information, see [Specifying a custom AMI](#emr-specify-custom-ami) and [Creating a custom AMI with an encrypted Amazon EBS root device volume](#emr-custom-ami-encrypted)\.

### Creating a custom AMI with an encrypted Amazon EBS root device volume<a name="emr-custom-ami-encrypted"></a>

To encrypt the Amazon EBS root device volume of an Amazon Linux AMI for Amazon EMR, copy a snapshot image from an unencrypted AMI to an encrypted target\. For information about creating encrypted EBS volumes, see [Amazon EBS encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) in the *Amazon EC2 User Guide for Linux Instances*\. The source AMI for the snapshot can be the base Amazon Linux AMI, or you can copy a snapshot from an AMI derived from the base Amazon Linux AMI that you customized\. 

**Note**  
Beginning with Amazon EMR version 5\.24\.0, you can use a security configuration option to encrypt EBS root device and storage volumes when you specify AWS KMS as your key provider\. For more information, see [Local disk encryption](emr-data-encryption-options.md#emr-encryption-localdisk)\.

You can use an external key provider or an AWS KMS key to encrypt the EBS root volume\. The service role that Amazon EMR uses \(usually the default `EMR_DefaultRole`\) must be allowed to encrypt and decrypt the volume, at minimum, for Amazon EMR to create a cluster using the AMI\. When using AWS KMS as the key provider, this means that the following actions must be allowed:
+ `kms:encrypt`
+ `kms:decrypt`
+ `kms:ReEncrypt*`
+ `kms:CreateGrant`
+ `kms:GenerateDataKeyWithoutPlaintext"`
+ `kms:DescribeKey"`

The simplest way to do this is to add the role as a key user as described in the following tutorial\. The following example policy statement is provided if you need to customize role policies\.

```
{ 
"Version": "2012-10-17", 
"Statement": [ 
   { 
   "Sid": "EmrDiskEncryptionPolicy", 
   "Effect": "Allow", 
   "Action": [ 
      "kms:Encrypt", 
      "kms:Decrypt", 
      "kms:ReEncrypt*", 
      "kms:CreateGrant", 
      "kms:GenerateDataKeyWithoutPlaintext", 
      "kms:DescribeKey" 
      ], 
   "Resource": [ 
      "*" 
      ] 
   } 
] 
}
```