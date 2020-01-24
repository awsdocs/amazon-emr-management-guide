# Supported Instance Types<a name="emr-supported-instance-types"></a>

The following table describes the instance types that Amazon EMR supports\. For more information, see [Amazon EC2 Instances](https://aws.amazon.com/ec2/instance-types/) and [Amazon Linux AMI Instance Type Matrix](https://aws.amazon.com/amazon-linux-ami/instance-type-matrix/)\.

Not all instance types are available in all regions\. If you create a cluster using an instance type that is not available, your cluster may fail to provision or may be stuck provisioning\. For information about instance availability, see the [Amazon EC2 Pricing Page](https://aws.amazon.com//ec2/pricing), click the link for your instance purchasing option, and filter by **Region** to see if the instance type you select from the list below is available in the region\.

Beginning with Amazon EMR release version 5\.13\.0, all instances use HVM virtualization and EBS\-backed storage for root volumes\. When using Amazon EMR release versions earlier than 5\.13\.0, some previous generation instances use PVM virtualization\. These are indicated in the table\. For more information, see [Linux AMI Virtualization Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html)\.

Some instance types support enhanced networking\. For more information, see [Enhanced Networking on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html)\.

Amazon EMR supports *Previous Generation Instances* to support applications that are optimized for these instances and have not yet been upgraded\. For more information about these instance types and upgrade paths, see [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation)\. 


| Instance Class | Instance Types | 
| --- | --- | 
| General purpose |  *m1\.medium¹* \| *m1\.large¹* \| *m1\.xlarge¹* \| *m2\.xlarge¹* \| *m2\.2xlarge¹* \| *m2\.4xlarge¹* \| *m3\.xlarge¹* \| *m3\.2xlarge¹* \| m4\.large \| m4\.xlarge \| m4\.2xlarge \| m4\.4xlarge \| m4\.10xlarge \| m4\.16xlarge \| m5\.xlarge³ \| m5\.2xlarge³ \| m5\.4xlarge³ \| m5\.8xlarge³ \| m5\.12xlarge³ \| m5\.16xlarge³ \| m5\.24xlarge³ \| m5a\.xlarge \| m5a\.2xlarge \| m5a\.4xlarge \| m5a\.8xlarge \| m5a\.12xlarge \| m5a\.16xlarge \| m5a\.24xlarge \| m5d\.xlarge³ \| m5d\.2xlarge³ \| m5d\.4xlarge³ \| m5d\.8xlarge³ \| m5d\.12xlarge³ \| m5d\.16xlarge³ \| m5d\.24xlarge³ m5a\-series instances are available when using Amazon EMR version 5\.20\.0 and later\.  | 
| Compute optimized |  *c1\.medium¹ ²* \| *c1\.xlarge¹* \| *c3\.xlarge¹* \| *c3\.2xlarge¹* \| *c3\.4xlarge¹* \| *c3\.8xlarge¹* \| c4\.large \| c4\.xlarge \| c4\.2xlarge \| c4\.4xlarge \| c4\.8xlarge \| c5\.xlarge³ \| c5\.2xlarge³ \| c5\.4xlarge³ \| c5\.9xlarge³ \| c5\.12xlarge³ \| c5\.18xlarge³ \| c5\.24xlarge³ \| c5d\.xlarge³ \| c5d\.2xlarge³ \| c5d\.4xlarge³ \| c5d\.9xlarge³ \| c5d\.18xlarge³ \| c5n\.xlarge \| c5n\.2xlarge \| c5n\.4xlarge \| c5n\.9xlarge \| c5n\.18xlarge \| *cc2\.8xlarge* \| z1d\.xlarge \| z1d\.2xlarge \| z1d\.3xlarge \| z1d\.6xlarge \| z1d\.12xlarge c5n\-series instances are available when using Amazon EMR version 5\.20\.0 and later\.  | 
| Memory optimized |  *r3\.xlarge* \| *r3\.2xlarge* \| *r3\.4xlarge* \| *r3\.8xlarge* \| r4\.xlarge \| r4\.2xlarge \| r4\.4xlarge \| r4\.8xlarge \| r4\.16xlarge \| r5\.xlarge³ \| r5\.2xlarge³ \| r5\.4xlarge³ \| r5\.8xlarge³ \| r5\.12xlarge³ \| r5\.16xlarge³ \| r5\.24xlarge³ \| r5a\.xlarge \| r5a\.2xlarge \| r5a\.4xlarge \| r5a\.8xlarge \| r5a\.12xlarge \| r5a\.16xlarge \| r5a\.24xlarge \| r5d\.xlarge³ \| r5d\.2xlarge³ \| r5d\.4xlarge³ \| r5d\.8xlarge³ \| r5d\.12xlarge³ \| r5d\.16xlarge³ \| r5d\.24xlarge³ \| *cr1\.8xlarge* r5a\-series instances are available when using Amazon EMR version 5\.20\.0 and later\.  | 
| Storage optimized |   h1\.2xlarge \| h1\.4xlarge \| h1\.8xlarge \| h1\.8xlarge \| *hs1\.8xlarge¹* \| *i2\.xlarge* \| *i2\.2xlarge* \| *i2\.4xlarge* \| *i2\.8xlarge* \| i3\.xlarge \| i3\.2xlarge \| i3\.4xlarge \| i3\.8xlarge \| i3\.16xlarge \| i3en\.xlarge \|i3en\.2xlarge \|i3en\.3xlarge \|i3en\.6xlarge \|i3en\.12xlarge \|i3en\.24xlarge \|d2\.xlarge \| d2\.2xlarge \| d2\.4xlarge \| d2\.8xlarge  i3\-series instances are available when using Amazon EMR version 5\.9\.0 and later\. i3en\-series instances are available when using Amazon EMR version 5\.25\.0 and later\.   | 
| GPU instances |  *cg1\.4xlarge* \| *g2\.2xlarge* \| g3\.4xlarge \| g3\.8xlarge \| g3\.16xlarge \| g3s\.xlarge \| p2\.xlarge \| p2\.8xlarge \| p2\.16xlarge \| p3\.2xlarge \| p3\.8xlarge \| p3\.16xlarge  NVIDIA and CUDA drivers are installed on P2 and P3 instance types by default\.  | 

¹*Uses PVM virtualization AMI with Amazon EMR release versions earlier than 5\.13\.0\. For more information, see [Linux AMI Virtualization Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html)\.*

²*Not supported in release version 5\.15\.0\.*

³*Supported in release version 5\.13\.0 and later\.*