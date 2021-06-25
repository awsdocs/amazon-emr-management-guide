# Launch an Amazon EMR cluster with Lake Formation<a name="emr-lf-launch-cluster"></a>

Before you launch an Amazon EMR cluster with AWS Lake Formation, you must complete the prerequisites in [Before you begin](emr-lf-prerequisites.md)\.

When you launch an Amazon EMR cluster with Lake Formation, you must specify the following items\. 
+ A **supported Amazon EMR release label**\. EMR with Lake Formation works with the Amazon EMR 5\.x series, starting with **5\.31\.0**\. EMR integration with Lake Formation is not yet available for the 6\.x series\. 
+ Your **security configuration** for Lake Formation\. If you need to create a security configuration, see [Configure EMR security](emr-lf-security.md)\. For instructions on how to select a security configuration when you create a cluster, see [Specify a security configuration for a cluster](emr-specify-security-configuration.md)\.
+ Your **customized EC2 instance profile**\. To set up a customized Amazon EC2 instance profile, see [Create a customized Amazon EC2 instance profile](emr-lf-create-instance-profile.md)\. For information about how to select your instance profile when you launch a cluster, see [Specify custom IAM roles when you create a cluster](emr-iam-roles-custom.md#emr-iam-roles-launch-jobflow)\.
+ Your **Kerberos settings**\. For more information, see [Kerberos settings for clusters](emr-kerberos-configure-settings.md#emr-kerberos-cluster-configuration)\.

After you launch your cluster, ensure you [update the callback or single sign\-on URL with your identity provider](#emr-lf-url) to direct users back to the master node of the cluster\.

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

## Update the callback or single sign\-on URL with your Identity Provider<a name="emr-lf-url"></a>

After you launch an Amazon EMR cluster with Lake Formation, you must update the callback or single\-sign\-on URL with your identity provider in order to direct users back to the master node of your cluster\.

1. Locate the public IP address of the master node and the master instance ID for your cluster in the EMR console \([https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\) or using the AWS CLI\.

1. Set up a callback URL in your identity provider \(IdP\) account:
   + When using AD FS as your IdP, complete the following steps:

     1. From the AD FS Management Console, go to **Relying Party Trusts**\.

     1. Right\-click the display name of your replying party trust, and choose **Properties**\.

     1. In the **Properties** window, choose the **Endpoints** tab\. 

     1. Select the temporary URL that you provided previously, then choose **edit**\. 

     1. In the **Edit Endpoint** window, update the Trusted URL with the correct DNS name for your master node\. 

     1. In the **Add an Endpoint** window, fill in the **Trusted URL** box with your master node public DNS\. For example, 

        ```
        https://ec2-11-111-11-111.compute-1.amazonaws.com:8442/gateway/knoxsso/api/v1/websso?pac4jCallback=true&client_name=SAML2Client
        ```

     1. Choose **OK**\.
   + When using Auth0 as your IdP, complete the following steps: 

     1. Go to https://auth0\.com/ and log in\.

     1. In the left panel, choose **Applications**\. 

     1. Select your previously created application\. 

     1. On the **Settings** tab, update **Allowed Callback URLs** with your master node public DNS\. 
   + When using Okta as your IdP, complete the following steps:

     1. Go to https://developer\.okta\.com/ and log in\.

     1. In the top right corner, choose **Admin**, then choose the **Applications** tab\. 

     1. Select your application name\.

     1. On the **General** tab under your application name, choose **SAML Settings**, then choose **Edit**\. 

     1. On the **Configure SAML** tab, update **Single\-sign on URL** with your master node public DNS\.