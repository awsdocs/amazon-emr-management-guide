# Set up a VPC to Host Clusters<a name="emr-vpc-host-job-flows"></a>

Before you can launch clusters in a VPC, you must create a VPC, and a subnet\. For public subnets, you must create an internet gateway and attach it to the subnet\. The following instructions describe how to create a VPC capable of hosting Amazon EMR clusters\. 

**To create a subnet to run Amazon EMR clusters**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation bar, select the region in which to run your cluster\.

1.  Choose **Start VPC Wizard**\.

1.  Choose the VPC configuration by selecting one of the following options: 
   + **VPC with a Single Public Subnet**—Select this option if the data used in the cluster is available on the internet \(for example, in Amazon S3 or Amazon RDS\)\.
   + **VPC with Public and Private subnets and Hardware VPN Access**—Select this option to use a private subnet or if data for your application is stored in your own network \(for example, in an Oracle database\)\. This option also allows you to include public subnets within the same VPC as private subnets\.

1. Confirm the VPC settings\. The images show both single public and private and public scenarios\.  
![\[Configuring VPC settings with public subnet\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpc-4-create-vpc-dialog2.png)  
![\[Configuring VPC settings with public and private subnets\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/vpcwizardprivate.png)
   + To work with Amazon EMR, the VPC with a public subnet must have both an internet gateway and a subnet\.

     For a VPC in a private subnet, all EC2 instances must at minimum have a route to Amazon EMR through the elastic network interface\. In the console, this is automatically configured for you\.
   + Use a private IP address space for your VPC to ensure proper DNS hostname resolution; otherwise, you may experience Amazon EMR cluster failures\. This includes the following IP address ranges: 
     + 10\.0\.0\.0 \- 10\.255\.255\.255
     + 172\.16\.0\.0 \- 172\.31\.255\.255
     + 192\.168\.0\.0 \- 192\.168\.255\.255
   + Choose **Use a NAT instance instead** and select options as appropriate\.
   + Optionally choose to **Add endpoints for S3 to your subnets**\.
   + Verify that **Enable DNS hostnames** is checked\. You have the option to enable DNS hostnames when you create the VPC\. To change the setting of DNS hostnames, select your VPC in the VPC list, then choose **Edit** in the details pane\. To create a DNS entry that does not include a domain name, create a value for **DHCP Options Set**, and then associate it with your VPC\. You cannot edit the domain name using the console after the DNS option set has been created\.

     For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\.
   + It is a best practice with Hadoop and related applications to ensure resolution of the fully qualified domain name \(FQDN\) for nodes\. To ensure proper DNS resolution, configure a VPC that includes a DHCP options set whose parameters are set to the following values:
     + **domain\-name** = **ec2\.internal**

       Use **ec2\.internal** if your region is US East \(N\. Virginia\)\. For other regions, use *region\-name***\.compute\.internal**\. For examples in `us-west-2`, use **us\-west\-2\.compute\.internal**\. For the AWS GovCloud \(US\-West\) region, use **us\-gov\-west\-1\.compute\.internal**\.
     + **domain\-name\-servers** = **AmazonProvidedDNS**

     For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

1. Choose **Create VPC**\. If you are creating a NAT instance, it may take a few minutes for this to complete\.

After the VPC is created, go to the **Subnets** page and note the identifier of one of the subnets of your VPC\. You use this information when you launch the EMR cluster into the VPC\.