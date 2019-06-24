# Working with Amazon Linux AMIs in Amazon EMR<a name="emr-ami"></a>

Amazon EMR uses an Amazon Linux Amazon Machine Image \(AMI\) to initialize Amazon EC2 instances when you create and launch a cluster\. The AMI contains the Amazon Linux operating system, other software, and the configurations required for each instance to host your cluster applications\.

By default, when you create a cluster, Amazon EMR uses a default Amazon Linux AMI that is created specifically for the Amazon EMR release version you use\. When you use Amazon EMR 5\.7\.0 or later, you can choose to specify a custom Amazon Linux AMI instead of the default Amazon Linux AMI for Amazon EMR\. A custom AMI allows you to encrypt the root device volume and to customize applications and configurations as an alternative to using bootstrap actions\.

Amazon EMR automatically attaches an Amazon EBS General Purpose SSD volume as the root device for all AMIs\. Using an EBS\-backed AMI enhances performance\. The EBS costs are pro\-rated by the hour based on the monthly Amazon EBS charges for gp2 volumes in the region where the cluster runs\. For example, the cost per hour for the root volume on each cluster instance in a region that charges $0\.10/GB/month is approximately $0\.00139 per hour \($0\.10/GB/month divided by 30 days divided by 24h times 10 GB\)\. Whether you use the default Amazon Linux AMI or a custom Amazon Linux AMI, you can specify the size of the EBS root device volume from 10\-100 GiB\.

For more information about Amazon Linux AMIs, see [Amazon Machine Images \(AMI\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)\. For more information about instance storage for Amazon EMR instances, see [Instance Storage](emr-plan-storage.md)\.

**Topics**
+ [Using the Default Amazon Linux AMI for Amazon EMR](emr-default-ami.md)
+ [Using a Custom AMI](emr-custom-ami.md)
+ [Specifying the Amazon EBS Root Device Volume Size](emr-custom-ami-boot-volume-size.md)