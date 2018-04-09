# Supported Instance Types<a name="emr-supported-instance-types"></a>

The following table describes the instance types that Amazon EMR supports\. For more information on these instance types, families, and virtualization types, see [Amazon EC2 Instances](https://aws.amazon.com/ec2/instance-types/) and [Amazon Linux AMI Instance Type Matrix](https://aws.amazon.com/amazon-linux-ami/instance-type-matrix/)\.

Not all instance types are available in all regions\. If you create a cluster using an instance type that is not available, your cluster may fail to provision or may be stuck provisioning\. For information about instance availability, see the [Amazon EC2 Pricing Page](https://aws.amazon.com//ec2/pricing), click the link for your instance purchasing option, and filter by **Region** to see if the instance type you select from the list below is available in the region\.

Amazon EMR supports *Previous Generation Instances* to support applications that are optimized for these instances and have not yet been upgraded\. For more information about these instances and upgrade paths, see [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation)\.


| Instance Class | Instance Types | 
| --- | --- | 
| General purpose |  *m1\.medium* \| *m1\.large* \| *m1\.xlarge* \| *m2\.xlarge* \| *m2\.2xlarge* \| *m2\.4xlarge* \| m3\.xlarge \| m3\.2xlarge \| m4\.large \| m4\.xlarge \| m4\.2xlarge \| m4\.4xlarge \| m4\.10xlarge \| m4\.16xlarge  | 
| Compute optimized |  *c1\.medium* \| *c1\.xlarge* \| c3\.xlarge \| c3\.2xlarge \| c3\.4xlarge \| c3\.8xlarge \| c4\.large \| c4\.xlarge \| c4\.2xlarge \| c4\.4xlarge \| c4\.8xlarge \| *cc2\.8xlarge*  | 
| Memory optimized |  r3\.xlarge \| r3\.2xlarge \| r3\.4xlarge \| r3\.8xlarge \| r4\.xlarge \| r4\.2xlarge \| r4\.4xlarge \| r4\.8xlarge \| r4\.16xlarge \| *cr1\.8xlarge*  | 
| Storage optimized |  *hs1\.8xlarge* \| *i2\.xlarge* \| *i2\.2xlarge* \| *i2\.4xlarge* \| *i2\.8xlarge* \| i3\.xlarge \| i3\.2xlarge \| i3\.4xlarge \| i3\.8xlarge \| i3\.16xlarge \| d2\.xlarge \| d2\.2xlarge \| d2\.4xlarge \| d2\.8xlarge  i3\-series instances available when using Amazon EMR version 5\.9\.0 and later\.   | 
| GPU instances |  *g2\.2xlarge* \| *cg1\.4xlarge* \| p2\.xlarge \| p2\.8xlarge \| p2\.16xlarge \| p3\.2xlarge \| p3\.8xlarge \| p3\.16xlarge  NVIDIA and CUDA drivers are installed on P2 and P3 instance types by default\.  | 

**Note**  
M4 and C4 instance types are only supported on Amazon EMR releases 3\.10\.0 and 4\.x or greater, and all new releases launch clusters with EBS\-backed storage for root volumes on all instance types\.

## Virtualization Types<a name="emr-instance-virtualization"></a>

The following table shows the virtualization type for each instance family used in Amazon EMR\. For more information, see [Linux AMI Virtualization Types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html) in the *Amazon EC2 User Guide for Linux Instances*\.


| Instance Family | Virtualization Types | 
| --- | --- | 
| c1 | PVM | 
| c3 | PVM | 
| c4 | HVM | 
| cc2 | HVM | 
| cg1 | HVM | 
| cr1 | HVM | 
| g2 | HVM | 
| hs1 | PVM | 
| i2 | HVM | 
| i3 | HVM | 
| m1 | PVM | 
| m2 | PVM | 
| m3 | PVM | 
| m4 | HVM | 
| p2 | HVM | 
| p3 | HVM | 
| r3 | HVM | 
| r4 | HVM | 

### Enhanced Networking<a name="emr-enhanced-networking"></a>

Some instance types support enhanced networking provided that the instance type selected uses the HVM virtualization type, as indicated in the previous table\. For more information about enhanced networking, see the following: 
+ [Linux AMI Virtualization Types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html)
+ [Enhanced Networking on Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html)
+ [Placement Groups](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html)