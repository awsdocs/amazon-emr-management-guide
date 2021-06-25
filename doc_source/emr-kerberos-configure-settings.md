# Security configuration and cluster settings for Kerberos on Amazon EMR<a name="emr-kerberos-configure-settings"></a>

When you create a Kerberized cluster, you specify the security configuration together with Kerberos attributes that are specific to the cluster\. You can't specify one set without the other, or an error occurs\.

This topic provides an overview of the configuration parameters available for Kerberos when you create a security configuration and a cluster\. In addition, CLI examples for creating compatible security configurations and clusters are provided for common architectures\.

## Kerberos settings for security configurations<a name="emr-kerberos-security-configuration"></a>

You can create a security configuration that specifies Kerberos attributes using the Amazon EMR console, the AWS CLI, or the EMR API\. The security configuration can also contain other security options, such as encryption\. For more information, see [Create a security configuration](emr-create-security-configuration.md)\.

Use the following references to understand the available security configuration settings for the Kerberos architecture that you choose\. Amazon EMR console settings are shown\. For corresponding CLI options, see [Specifying Kerberos settings using the AWS CLI](emr-create-security-configuration.md#emr-kerberos-cli-parameters) or [Configuration examples](emr-kerberos-config-examples.md)\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-kerberos-configure-settings.html)

## Kerberos settings for clusters<a name="emr-kerberos-cluster-configuration"></a>

You can specify Kerberos settings when you create a cluster using the Amazon EMR console, the AWS CLI, or the EMR API\.

Use the following references to understand the available cluster configuration settings for the Kerberos architecture that you choose\. Amazon EMR console settings are shown\. For corresponding CLI options, see [Configuration examples](emr-kerberos-config-examples.md)\.


| Parameter | Description | 
| --- | --- | 
|  Realm  |  The Kerberos realm name for the cluster\. The Kerberos convention is to set this to be the same as the domain name, but in uppercase\. For example, for the domain `ec2.internal`, using `EC2.INTERNAL` as the realm name\.  | 
|  KDC admin password  |  The password used within the cluster for `kadmin` or `kadmin.local`\. These are command\-line interfaces to the Kerberos V5 administration system, which maintains Kerberos principals, password policies, and keytabs for the cluster\.   | 
|  Cross\-realm trust principal password \(optional\)  |  Required when establishing a cross\-realm trust\. The cross\-realm principal password, which must be identical across realms\. Use a strong password\.  | 
|  Active Directory domain join user \(optional\)  |  Required when using Active Directory in a cross\-realm trust\. This is the user logon name of an Active Directory account with permission to join computers to the domain\. Amazon EMR uses this identity to join the cluster to the domain\. For more information, see [Step 3: Add user accounts to the domain for the EMR Cluster](emr-kerberos-cross-realm.md#emr-kerberos-ad-users)\.  | 
|  Active Directory domain join password \(optional\)  |  The password for the Active Directory domain join user\. For more information, see [Step 3: Add user accounts to the domain for the EMR Cluster](emr-kerberos-cross-realm.md#emr-kerberos-ad-users)\.  | 