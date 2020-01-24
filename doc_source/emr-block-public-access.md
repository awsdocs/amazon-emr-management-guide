# Using Amazon EMR Block Public Access<a name="emr-block-public-access"></a>

Amazon EMR block public access prevents a cluster from launching when any security group associated with the cluster has a rule that allows inbound traffic from IPv4 0\.0\.0\.0/0 or IPv6 ::/0 \(public access\) on a port, unless the port has been specified as an exception\. Port 22 is an exception by default\. You can configure exceptions to allow public access on a port or range of ports\. In addition, you can enable or disable block public access\. We recommend that you enable it\.

Block public access is enabled and configured for each AWS Region for your AWS account\. In other words, each Region has a block public access configuration that applies to all clusters created by your account in that Region\.

**Note**  
For accounts that created clusters in a Region before November 25, 2019, block public access is disabled by default in that Region\. To use this feature, you must manually enable and configure it\. For accounts that did not create an EMR cluster in a Region before this date, block public access is enabled by default in that Region\. 

## Configure Block Public Access<a name="configure-block-public-access"></a>

You can enable and disable block public access settings using the AWS Management Console, the AWS CLI, and the Amazon EMR API\. Settings apply across your account on a Region\-by\-Region basis\.

### To configure block public access using the AWS Management Console<a name="configure-bpa-console"></a>

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the navigation bar, make sure that the **Region** you want to configure is selected\.

1. Choose **Block public access**\.

1. Under **Block public access settings**, complete the following steps\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-block-public-access.html)

### To configure block public access using the AWS CLI<a name="configure-bpa-cli"></a>

Use the `aws emr put-block-public-access-configuration` command to configure block public access as shown in the following examples\.


| To\.\.\. | Do this\.\.\. | 
| --- | --- | 
|  Turn block public access on  |  Set `BlockPublicSecurityGroupRules` to `true` as shown in the following example\. For the cluster to launch, no security group associated with a cluster can have an inbound rule that allows public access\. <pre>aws emr put-block-public-access-configuration --block-public-access-configuration BlockPublicSecurityGroupRules=true</pre>  | 
|  Turn block public access off  |  Set `BlockPublicSecurityGroupRules` to `false` as shown in the following example\. Security groups associated with a cluster can have inbound rules that allow public access on any port\. We do not recommend this configuration\. <pre>aws emr put-block-public-access-configuration --block-public-access-configuration BlockPublicSecurityGroupRules=false</pre>  | 
|  Turn block public access on and specify ports as exceptions  |  The following example turns on block public access, and specifies Port 22 and Ports 100\-101 as exceptions\. This allows clusters to be created if an associated security group has an inbound rule that allows public access on Port 22, Port 100, or Port 101\. <pre>aws emr put-block-public-access-configuration --block-public-access-configuration  '{ "BlockPublicSecurityGroupRules": true, "PermittedPublicSecurityGroupRuleRanges": [ { "MinRange": 22, "MaxRange": 22 }, { "MinRange": 100, "MaxRange": 101 } ] }'</pre>  | 