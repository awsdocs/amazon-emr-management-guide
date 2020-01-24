# Specifying the Amazon EBS Root Device Volume Size<a name="emr-custom-ami-boot-volume-size"></a>

This option is available only with Amazon EMR version 4\.x and later\. You can specify the volume size from 10 GiB \(the default\) up to 100 GiB when you create a cluster using the AWS Management Console, the AWS CLI, or the Amazon EMR API\. This sizing applies only to the EBS root device volume and applies to all instances in the cluster\. It does not apply to storage volumes, which you specify separately for each instance type when you create your cluster\.

**Note**  
If you use the default AMI, Amazon EMR attaches General Purpose SSD \(gp2\) as the root device volume type\. A custom AMI may have a different root device volume type\. For more information, see [Specifying a Custom AMI](emr-custom-ami.md#emr-specify-custom-ami)\.

The cost of the EBS root device volume is pro\-rated by the hour, based on the monthly EBS charges for that volume type in the region where the cluster runs\. The same is true of storage volumes\. Charges are in GB, but you specify the size of the root volume in GiB, so you may want to consider this in your estimates \(1 GB is 0\.931323 GiB\)\. To estimate the charges associated with EBS root device volumes in your cluster, use the following formula:

\($EBS GB­month\)×0\.931323÷30÷24×EMR\_EBSRootGiB×InstanceCount

For example, take a cluster that has a master node, a core node, and uses the base Amazon Linux AMI, with the default 10 GiB root device volume\. If the EBS cost in the region is USD$0\.10/GB\-Month, that works out to be approximately $0\.00129 per instance per hour and $0\.00258 per hour for the cluster \($0\.10 GB\-month divided by 30 days, divided by 24 hours, multiplied by 10 GB, multiplied by 2 cluster instances\)\.

**To specify the EBS root device volume size using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. Under **Software Configuration**, for **Release**, choose a 4\.x or 5\.x value and other options as appropriate for your application, and choose **Next**\.

1. Under **Hardware Configuration**, for **Root device EBS volume size**, enter a value between 10 GiB and 100 GiB\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ebs-volume-size.png)

**To specify the EBS root device volume size using the AWS CLI**
+ Use the `--ebs-root-volume-size` parameter of the [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html) command as shown in the following example\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --release-label emr-5.7.0 \
  --ebs-root-volume-size 20 --instance-groups InstanceGroupType=MASTER,\
  InstanceCount=1,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=2,InstanceType=m5.xlarge
  ```