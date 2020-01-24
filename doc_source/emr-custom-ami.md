# Using a Custom AMI<a name="emr-custom-ami"></a>

When you use Amazon EMR 5\.7\.0 or later, you can choose to specify a custom Amazon Linux AMI instead of the default Amazon Linux AMI for Amazon EMR\. A custom AMI is useful if you want to do the following:
+ Pre\-install applications and perform other customizations instead of using bootstrap actions\. This can improve cluster start time and streamline the startup work flow\. For more information and an example, see [Creating a Custom Amazon Linux AMI from a Preconfigured Instance](#emr-custom-ami-preconfigure)\.
+ Implement more sophisticated cluster and node configurations than bootstrap actions allow\.
+ Encrypt the EBS root device volumes \(boot volumes\) of EC2 instances in your cluster if you are using an Amazon EMR version earlier than 5\.24\.0\. For more information, see [Creating a Custom AMI with an Encrypted Amazon EBS Root Device Volume](#emr-custom-ami-encrypted)\.
**Note**  
Beginning with Amazon EMR version 5\.24\.0, you can use a security configuration option to encrypt EBS root device and storage volumes when you specify AWS KMS as your key provider\. For more information, see [Local Disk Encryption](emr-data-encryption-options.md#emr-encryption-localdisk)\.

## Best Practices and Considerations<a name="emr-custom-ami-considerations"></a>

When you create a custom AMI for Amazon EMR, consider the following:
+ You must use an Amazon Linux AMI\. Amazon Linux 2 AMIs are not supported\. Only 64\-bit Amazon Linux AMIs are supported\. Amazon Linux AMIs with multiple Amazon EBS volumes are not supported\.
+ Base your customization on the most recent EBS\-backed [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\. For a list of Amazon Linux AMIs and corresponding AMI IDs, see [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\.
+ Do not copy a snapshot of an existing Amazon EMR instance to create a custom AMI\. This causes errors\.
+ Only the HVM virtualization type and instances compatible with Amazon EMR are supported\. Be sure to select the HVM image and an instance type compatible with Amazon EMR as you go through the AMI customization process\. For compatible instances and virtualization types, see [Supported Instance Types](emr-supported-instance-types.md)\.
+ Your service role must have launch permissions on the AMI, so either the AMI must be public, or you must be the owner of the AMI or have it shared with you by the owner\.
+ Creating users on the AMI with the same name as applications causes errors \(for example, `hadoop`, `hdfs`, `yarn`, or `spark`\)\.
+ The contents of `/tmp`, `/var`, and `/emr`—if they exist on the AMI—are moved to `/mnt/tmp`, `/mnt/var`, and `/mnt/emr` respectively during startup\. Files are preserved, but if there is a large amount of data, startup may take longer than expected\.
+ If you use a custom Amazon Linux AMI based on an Amazon Linux AMI with a creation date of 2018\-08\-11, the Oozie server fails to start\. If you use Oozie, create a custom AMI based on an Amazon Linux AMI ID with a different creation date\. You can use the following AWS CLI command to return a list of Image IDs for all HVM Amazon Linux AMIs with a 2018\.03 version, along with the release date, so that you can choose an appropriate Amazon Linux AMI as your base\. Replace MyRegion with your region identifier, such as us\-west\-2\.

  ```
  aws ec2 --region MyRegion describe-images --owner amazon --query 'Images[?Name!=`null`]|[?starts_with(Name, `amzn-ami-hvm-2018.03`) == `true`].[CreationDate,ImageId,Name]' --output text | sort -rk1
  ```

For more information, see [Creating an Amazon EBS\-Backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Specifying a Custom AMI<a name="emr-specify-custom-ami"></a>

You can specify a custom AMI ID when you create a cluster using the AWS Management Console, AWS CLI, [Amazon CloudWatch](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticmapreduce-cluster.html), or the Amazon EMR API\. The AMI must exist in the same AWS Region where you create the cluster\.<a name="emr-specify-custom-ami-console"></a>

**To specify a custom AMI using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Under **Software Configuration**, for **Release**, choose **emr\-5\.7\.0 **or later and then choose other options as appropriate for your application\. Choose **Next**\.

1. Select values under **Hardware Configuration** that are appropriate for your application, and choose **Next**\.

1. Under **Additional Options**, for **Custom AMI ID**, enter a value and leave the update option selected\. For more information about changing the update option, see [Managing AMI Package Repository Updates](#emr-custom-ami-package-update)\.  
![\[\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-custom-ami.png)

1. To launch the cluster, choose **Next** and complete other configuration options\.

**To specify a custom AMI using the AWS CLI**
+ Use the `--custom-ami-id` parameter to specify the AMI ID when you run the `aws emr [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html)` command\.

  The following example specifies a cluster that uses a custom AMI with a 20 GiB boot volume\. For more information, see [Specifying the Amazon EBS Root Device Volume Size](emr-custom-ami-boot-volume-size.md)\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Cluster with My Custom AMI" \
  --custom-ami-id MyAmiID --ebs-root-volume-size 20 \
  --release-label emr-5.7.0 --use-default-roles \
  --instance-count 2 --instance-type m5.xlarge
  ```

## Managing AMI Package Repository Updates<a name="emr-custom-ami-package-update"></a>

On first boot, by default, Amazon Linux AMIs connect to package repositories to install security updates before other services start\. Depending on your requirements, you may choose to disable these updates when you specify a custom AMI for Amazon EMR\. The option to disable this feature is available only when you use a custom AMI\.

**Warning**  
We strongly recommend that you choose to update all installed packages on reboot when you specify a custom AMI\. Choosing not to update packages creates additional security risks\.

Using the AWS Management Console, you can select the option to disable updates when you choose **Custom AMI ID**\. 

Using the AWS CLI, you can specify `--repo-upgrade-on-boot NONE` along with `--custom-ami-id` when using the create\-cluster command\. 

Using the Amazon EMR API, you can specify `NONE` for the [RepoUpgradeOnBoot](https://docs.aws.amazon.com/ElasticMapReduce/latest/API/API_RunJobFlow.html#EMR-RunJobFlow-request-RepoUpgradeOnBoot) parameter\.

## Creating a Custom Amazon Linux AMI from a Preconfigured Instance<a name="emr-custom-ami-preconfigure"></a>

The basic steps for pre\-installing software and performing other configurations to create a custom Amazon Linux AMI for Amazon EMR are as follows:
+ Launch an instance from the base Amazon Linux AMI\.
+ Connect to the instance to install software and perform other customizations\.
+ Create a new image \(AMI snapshot\) of the instance you configured\.

After you create the image based on your customized instance, you can copy that image to an encrypted target as described in [Creating a Custom AMI with an Encrypted Amazon EBS Root Device Volume](#emr-custom-ami-encrypted)\.

### Tutorial: Creating an AMI from an Instance with Custom Software Installed<a name="emr-custom-ami-preconfigure-walkthrough"></a>

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

1. Use an SSH connection that lets you run shell commands on your Linux instance\. For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

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

  The `imageID` output value is used when you launch the cluster or create an encrypted snapshot\. For more information, see [Specifying a Custom AMI](#emr-specify-custom-ami) and [Creating a Custom AMI with an Encrypted Amazon EBS Root Device Volume](#emr-custom-ami-encrypted)\.

## Creating a Custom AMI with an Encrypted Amazon EBS Root Device Volume<a name="emr-custom-ami-encrypted"></a>

To encrypt the Amazon EBS root device volume of an Amazon Linux AMI for Amazon EMR, copy a snapshot image from an unencrypted AMI to an encrypted target\. For information about creating encrypted EBS volumes, see [Amazon EBS encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) in the *Amazon EC2 User Guide for Linux Instances*\. The source AMI for the snapshot can be the base Amazon Linux AMI, or you can copy a snapshot from an AMI derived from the base Amazon Linux AMI that you customized\. 

**Note**  
Beginning with Amazon EMR version 5\.24\.0, you can use a security configuration option to encrypt EBS root device and storage volumes when you specify AWS KMS as your key provider\. For more information, see [Local Disk Encryption](emr-data-encryption-options.md#emr-encryption-localdisk)\.

You can use an external key provider or an AWS customer master key \(CMK\) to encrypt the EBS root volume\. The service role that Amazon EMR uses \(usually the default `EMR_DefaultRole`\) must be allowed to encrypt and decrypt the volume, at minimum, for Amazon EMR to create a cluster using the AMI\. When using AWS KMS as the key provider, this means that the following actions must be allowed:
+ `kms:encrypt`
+ `kms:decrypt`
+ `kms:ReEncrypt*`
+ `kms:CreateGrant`
+ `kms:GenerateDataKeyWithoutPlaintext"`
+ `kms:DescribeKey"`

The easiest way to do this is to add the role as a key user as described in the following tutorial\. The following example policy statement is provided if you need to customize role policies\.

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

### Tutorial: Creating a Custom AMI with an Encrypted Root Device Volume Using a KMS CMK<a name="emr-custom-ami-encrypted-walkthrough"></a>

The first step in this example is to find the ARN of a KMS CMK or create a new one\. For more information about creating keys, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. The following procedure shows you how to add the default service role, `EMR_DefaultRole`, as a key user to the key policy\. Write down the **ARN** value for the key as you create or edit it\. You use the ARN later, when you create the AMI\.

**To add the service role for Amazon EC2 to the list of encryption key users using the console**

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Choose the alias of the CMK to use\.

1. On the key details page under **Key Users**, choose **Add**\.

1. In the **Attach** dialog box, choose the Amazon EMR service role\. The name of the default role is `EMR_DefaultRole`\.

1. Choose **Attach**\.

**To create an encrypted AMI using the AWS CLI**
+ Use the `aws ec2 copy-image` command from the AWS CLI to create an AMI with an encrypted EBS root device volume and the key that you modified\. Replace the `--kms-key-id` value specified with the full ARN of the key that you created or modified earlier\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws ec2 copy-image --source-image-id MyAmiId \
  --source-region us-west-2 --name MyEncryptedEMRAmi \
  --encrypted --kms-key-id arn:aws:kms:us-west-2:12345678910:key/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  ```

The output of the command provides the ID of the AMI that you created, which you can specify when you create a cluster\. For more information, see [Specifying a Custom AMI](#emr-specify-custom-ami)\. You can also choose to customize this AMI by installing software and performing other configurations\. For more information, see [Creating a Custom Amazon Linux AMI from a Preconfigured Instance](#emr-custom-ami-preconfigure)\.