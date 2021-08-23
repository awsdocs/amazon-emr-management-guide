# Working with Amazon Linux AMIs in Amazon EMR<a name="emr-ami"></a>

Amazon EMR uses an Amazon Linux Amazon Machine Image \(AMI\) to initialize Amazon EC2 instances when you create and launch a cluster\. The AMI contains the Amazon Linux operating system, other software, and the configurations required for each instance to host your cluster applications\.

By default, when you create a cluster, Amazon EMR uses a default Amazon Linux AMI that is created specifically for the Amazon EMR release version you use\. For more information about the default Amazon Linux AMI, see [Using the default Amazon Linux AMI for Amazon EMR](emr-default-ami.md)\. When you use Amazon EMR 5\.7\.0 or later, you can choose to specify a custom Amazon Linux AMI instead of the default Amazon Linux AMI for Amazon EMR\. A custom AMI allows you to encrypt the root device volume and to customize applications and configurations as an alternative to using bootstrap actions\. You can specify a custom AMI for each instance type in the instance group or instance fleet configuration of an Amazon EMR cluster\. Instance\-level custom AMI support gives you the flexibility to use more than one architecture type in a cluster\. See [Using a custom AMI](emr-custom-ami.md)\.

Amazon EMR automatically attaches an Amazon EBS General Purpose SSD volume as the root device for all AMIs\. Using an EBS\-backed AMI enhances performance\. The Amazon EBS costs are pro\-rated by the hour based on the monthly Amazon EBS charges for gp2 volumes in the Region where the cluster runs\. For example, the cost per hour for the root volume on each cluster instance in a Region that charges $0\.10/GB/month is approximately $0\.00139 per hour \($0\.10/GB/month divided by 30 days divided by 24 hours times 10 GB\)\. Whether you use the default Amazon Linux AMI or a custom Amazon Linux AMI, you can specify the size of the Amazon EBS root device volume from 10\-100 GiB\.

For more information about Amazon Linux AMIs, see [Amazon Machine Images \(AMI\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)\. For more information about instance storage for Amazon EMR instances, see [Instance storage](emr-plan-storage.md)\.

**Topics**
+ [Using the default Amazon Linux AMI for Amazon EMR](emr-default-ami.md)
+ [Using a custom AMI](emr-custom-ami.md)
+ [Specifying a custom AMI](emr-specify-custom-ami.md)
+ [Specifying the Amazon EBS root device volume size](emr-custom-ami-boot-volume-size.md)