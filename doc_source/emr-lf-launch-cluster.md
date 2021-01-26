# Launch an Amazon EMR Cluster with Lake Formation<a name="emr-lf-launch-cluster"></a>

When you launch an Amazon EMR cluster with Lake Formation, you will specify the following items:
+ A **supported Amazon EMR release label**\. EMR with Lake Formation works with the Amazon EMR 5\.x series, starting with **5\.31\.0**\. EMR integration with Lake Formation is not yet available for the 6\.x series\. 
+ The **security configuration** you created for Lake Formation\. For instructions on how to select a security configuration when you create a cluster, see [Specify a Security Configuration for a Cluster](emr-specify-security-configuration.md)\.
+ Your **customized EC2 instance profile**\. For instructions, see [Specify Custom IAM Roles When You Create a Cluster](emr-iam-roles-custom.md#emr-iam-roles-launch-jobflow)\.
+ Your **Kerberos settings**\. For more information, see [Kerberos Settings for Clusters](emr-kerberos-configure-settings.md#emr-kerberos-cluster-configuration)\.

If you have not prepared the items listed above, see [Before You Begin](emr-lf-prerequisites.md) to complete these prerequisites\.

After you launch your cluster, ensure you [update the callback or single sign\-on URL with your identity provider](emr-lf-url.md) to direct users back to the master node of the cluster\.

## Example: Create an EMR Cluster with Lake Formation using the AWS CLI<a name="emr-lf-launch-cluster-example"></a>

The following example AWS CLI command launches an Amazon EMR cluster with Zeppelin integrated with AWS Lake Formation\. It includes specified values for `--kerberos-attributes`, `--security-configuration`, and `InstanceProfile` as required for EMR integration with Lake Formation\.

```
aws emr create-cluster --region us-east-1 \
--release-label emr-5.31.0 \
--use-default-roles \
--instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m4.xlarge \
InstanceGroupType=CORE,InstanceCount=1,InstanceType=m4.xlarge \
--applications Name=Zeppelin Name=Livy \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyClusterKDCAdminPassword \
--ec2-attributes KeyName=EC2_KEY_PAIR,SubnetId=subnet-00xxxxxxxxxxxxx11,InstanceProfile=MyCustomEC2InstanceProfile \
--security-configuration security-configuration \
--name cluster-name
```