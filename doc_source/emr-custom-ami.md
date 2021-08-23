# Using a custom AMI<a name="emr-custom-ami"></a>

When you use Amazon EMR 5\.7\.0 or later, you can choose to specify a custom Amazon Linux AMI instead of the default Amazon Linux AMI for Amazon EMR\. A custom AMI is useful if you want to do the following:
+ Pre\-install applications and perform other customizations instead of using bootstrap actions\. This can improve cluster start time and streamline the startup work flow\. For more information and an example, see [Creating a custom Amazon Linux AMI from a preconfigured instance](emr-specify-custom-ami.md#emr-custom-ami-preconfigure)\.
+ Implement more sophisticated cluster and node configurations than bootstrap actions allow\.
+ Encrypt the EBS root device volumes \(boot volumes\) of EC2 instances in your cluster if you are using an Amazon EMR version earlier than 5\.24\.0\. As with the default AMI, the minimum root volume size for a custom AMI is 10 GiB\. For more information, see [Creating a custom AMI with an encrypted Amazon EBS root device volume](emr-specify-custom-ami.md#emr-custom-ami-encrypted)\.
**Note**  
Beginning with Amazon EMR version 5\.24\.0, you can use a security configuration option to encrypt EBS root device and storage volumes when you specify AWS KMS as your key provider\. For more information, see [Local disk encryption](emr-data-encryption-options.md#emr-encryption-localdisk)\.

**Important**  
Amazon EMR clusters that are running Amazon Linux or Amazon Linux 2 AMIs \(Amazon Linux Machine Images\) use default Amazon Linux behavior, and do not automatically download and install important and critical kernel updates that require a reboot\. This is the same behavior as other Amazon EC2 instances running the default Amazon Linux AMI\. If new Amazon Linux software updates that require a reboot \(such as, kernel, NVIDIA, and CUDA updates\) become available after an Amazon EMR version is released, Amazon EMR cluster instances running the default AMI do not automatically download and install those updates\. To get kernel updates, you can [customize your Amazon EMR AMI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html) to [use the latest Amazon Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html)\.

## Cluster\-level custom AMI and instance\-level custom AMIs<a name="emr-custom-ami-types"></a>

You can assign a single cluster\-level custom AMI to the entire cluster, or you can assign instance\-level custom AMIs to each instance node in your cluster\. You cannot launch a cluster with both a cluster\-level custom AMI and instance\-level custom AMIs\.

For clusters running Amazon EMR release 5\.7\.0 and later, or 6\.0 and later, you can specify a different instance\-level custom Amazon Linux AMI for each instance type in an instance fleet or instance group\. For example, you can configure x86\_64 architecture \(m5\.xlarge\) and arm64 architecture \(m6g\.xlarge\) instances in the same instance fleet or instance group\. Each instance type would use a unique custom AMI that matches the instance type architecture\. In this example, you would configure an m5\.xlarge instance type, which has an x86\_64 architecture, with an x86\_64 architecture instance\-level custom AMI\. Similarly, the m6g\.xlarge instance type, which has arm64 architecture, would be matched with a corresponding arm64 architecture instance\-level custom AMI\. For more information about identifying a Linux AMI for your instance type, see [Find a Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Linux Instances*\.


**Comparison of cluster\-level custom AMI and instance\-level custom AMI features**  

| Feature | Cluster\-level custom AMI | Instance\-level custom AMI | 
| --- | --- | --- | 
|  Can pre\-install applications and perform other customizations instead of using bootstrap actions\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Can implement more sophisticated cluster and node configurations than bootstrap actions allow\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Can encrypt the EBS root device volumes of cluster EC2 instances for Amazon EMR versions earlier than 5\.24\.0\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Can use both x86 and ARM \(Graviton\) architectures with custom AMIs in the same cluster\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-no.png) ☓ No  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Must specify one custom AMI per instance type in the cluster\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-no.png) ☓ No  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Custom AMI can be specified when creating a cluster\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Custom AMI can be specified when adding an instance group or instance fleet to a running cluster\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-no.png) ☓ No  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-yes.png) ✓ Yes  | 
|  Can run EMR AMI and custom AMI instances in the same cluster\.  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-no.png) ☓ No  |   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/icon-no.png) ☓ No  | 

## Best practices and considerations<a name="emr-custom-ami-considerations"></a>

When you create a custom AMI for Amazon EMR, consider the following:
+ Amazon EMR 5\.30\.0 and later, and the Amazon EMR 6\.x series are based on Amazon Linux 2\. For these Amazon EMR versions, you need to use images based on Amazon Linux 2 for custom AMIs\. To find a base custom AMI, see [Finding a Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html)\.
+ For Amazon EMR versions earlier than 5\.30\.0 and 6\.x, Amazon Linux 2 AMIs are not supported\. You must use a 64\-bit Amazon Linux AMI\.
+ Amazon Linux AMIs with multiple Amazon EBS volumes are not supported\.
+ Base your customization on the most recent EBS\-backed [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\. For a list of Amazon Linux AMIs and corresponding AMI IDs, see [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\.
+ Do not copy a snapshot of an existing Amazon EMR instance to create a custom AMI\. This causes errors\.
+ Only the HVM virtualization type and instances compatible with Amazon EMR are supported\. Be sure to select the HVM image and an instance type compatible with Amazon EMR as you go through the AMI customization process\. For compatible instances and virtualization types, see [Supported instance types](emr-supported-instance-types.md)\.
+ Your service role must have launch permissions on the AMI, so either the AMI must be public, or you must be the owner of the AMI or have it shared with you by the owner\.
+ Creating users on the AMI with the same name as applications causes errors \(for example, `hadoop`, `hdfs`, `yarn`, or `spark`\)\.
+ The contents of `/tmp`, `/var`, and `/emr` \(if they exist on the AMI\) are moved to `/mnt/tmp`, `/mnt/var`, and `/mnt/emr` respectively during startup\. Files are preserved, but if there is a large amount of data, startup may take longer than expected\.
+ If you use a custom Amazon Linux AMI based on an Amazon Linux AMI with a creation date of 2018\-08\-11, the Oozie server fails to start\. If you use Oozie, create a custom AMI based on an Amazon Linux AMI ID with a different creation date\. You can use the following AWS CLI command to return a list of Image IDs for all HVM Amazon Linux AMIs with a 2018\.03 version, along with the release date, so that you can choose an appropriate Amazon Linux AMI as your base\. Replace MyRegion with your Region identifier, such as us\-west\-2\.

  ```
  aws ec2 --region MyRegion describe-images --owner amazon --query 'Images[?Name!=`null`]|[?starts_with(Name, `amzn-ami-hvm-2018.03`) == `true`].[CreationDate,ImageId,Name]' --output text | sort -rk1
  ```
+ In cases where you use a VPC with a non\-standard domain name and AmazonProvidedDNS, you should not use the `rotate` option in the Operating Systems DNS configuration\.

For more information, see [Creating an Amazon EBS\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances*\.