# Configure Kerberos<a name="emr-kerberos-configure"></a>

Set up Kerberos on Amazon EMR by following these steps\.

## Step 1: Create a Security Configuration that Enables Kerberos and Optional Cross\-Realm Trust Configuration<a name="emr-kerberos-security-configuration"></a>

You can create a security configuration that specifies Kerberos attributes using the Amazon EMR console, the AWS CLI, or the EMR API\. The security configuration can also contain other security options, such as encryption\. For more information, see [Create a Security Configuration](emr-create-security-configuration.md)\.

When you create a Kerberized cluster, you specify the security configuration together with Kerberos attributes that are specific to the cluster\. You can't specify one set without the other, or an error occurs\.

The following Kerberos parameters are set using the security configuration:


| Parameter | Description | 
| --- | --- | 
|  **Enable Kerberos**  | Specifies that Kerberos is enabled for clusters that use this security configuration\. If a cluster uses this security configuration, the cluster must also have Kerberos settings specified or an error occurs\. | 
|   **Ticket Lifetime**  |  Specifies the period for which a Kerberos ticket issued by the cluster\-dedicated KDC is valid\. Ticket lifetimes are limited for security reasons\. Cluster applications and services auto\-renew tickets after they expire\. Users who connect to the cluster over SSH using Kerberos credentials need to run `kinit` from the master node command line to renew after a ticket expires\.  | 
|  **Cross\-realm trust**  |  If you provide a cross\-realm trust configuration, principals \(typically users\) from another realm are authenticated to clusters that use this configuration\. Additional configuration in the other Kerberos realm is also required\. For more information, see [Configure a Cross\-Realm Trust](emr-kerberos-cross-realm.md)\.  | 
|   **Realm**  |  Specifies the Kerberos realm name of the other realm in the trust relationship\. Any string can be used, but by convention, this is typically the same as the Domain, but in all capital letters\.  | 
|   **Domain**  |  Specifies the domain name of the other realm in the trust relationship\.  | 
|   **Admin server**  |  Specifies the fully qualified domain name \(FQDN\) of the admin server in the other realm of the trust relationship\. The admin server and KDC server typically run on the same machine with the same FQDN, but communicate on different ports\. If no port is specified, port 749 is used, which is the Kerberos default\. Optionally, you can specify the port \(for example, domain\.example\.com:749\)\.  | 
|   **KDC server**  |  Specifies the fully qualified domain name \(FQDN\) of the KDC server in the other realm of the trust relationship\. The KDC server and admin server typically run on the same machine with the same FQDN, but communicate on different ports\. If no port is specified, port 88 is used, which is the Kerberos default\. Optionally, you can specify the port \(for example, domain\.example\.com:88\)\.  | 

The following examples demonstrate a security configuration with the same Kerberos attributes\. The first example shows the security configuration created using the Amazon EMR console\. The second example creates the same security configuration using a JSON file, which is referenced using the `create-security-configuration` command from the AWS CLI\. 

In the example, the KDC and admin services in the cross\-realm trust are hosted on the same server, `ad.domain.com`\. The default Kerberos ports of 749 for the KDC and 88 for administrative server are used\. If your application uses customized ports, use the form *ad\.domain\.com*:*portnumber*\.

**Example Example Security Configuration with Kerberos Settings Using the Console**  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-sec-cfg.png)

**Example Example Security Configuration with Kerberos Settings Using the AWS CLI**  
The following `create-security-configuration` command creates a security configuration named `KerberosSecurityConfig` based on a JSON file, `MyKerberosSecurityConfig.json` saved locally in the same directory where you run the command\.  

```
aws emr create-security-configuration --name "KerberosSecurityConfiguration" --security-configuration file://MyKerberosSecurityConfig.json
```
The `MyKerberosSecurityConfig.json` uses the `AuthenticationConfiguration` object as shown below to specify Kerberos settings, which correspond to the console settings above\. The security configuration is later referenced in the example `create-cluster` command [below](#emr-cli-create-cluster-kerberos-example)\.  

```
{
  "AuthenticationConfiguration": {
    "KerberosConfiguration": {
      "Provider": "ClusterDedicatedKdc",
      "ClusterDedicatedKdcConfiguration": {
        "TicketLifetimeInHours": 24,
        "CrossRealmTrustConfiguration": {
          "Realm": "AD.DOMAIN.COM",
          "Domain": "ad.domain.com",
          "AdminServer": "ad.domain.com",
          "KdcServer": "ad.domain.com"
        }
      }
    }
  }
}
```

## Step 2: Configure Kerberos Attributes for a Cluster<a name="emr-kerberos-configuration-cluster-configuration"></a>

Specify Kerberos attributes for a particular cluster along with the Kerberos security configuration when you create the cluster\. You must specify the cluster Kerberos settings and a Kerberos security configuration together, or an error occurs\. You can use the Amazon EMR console, the AWS CLI, or the Amazon EMR API\. 

The following Kerberos attributes are specified using the cluster configuration:


| Attribute | Description | 
| --- | --- | 
|  Realm  |  The Kerberos realm name for the cluster\. The Kerberos convention is to set this to be the same as the domain name, but in uppercase\. For example, for the domain `ec2.internal`, using `EC2.INTERNAL` as the realm name\.  | 
|  KDC admin password  |  The password used within the cluster for `kadmin` or `kadmin.local`\. These are command\-line interfaces to the Kerberos V5 administration system, which maintains Kerberos principals, password policies, and keytabs for the cluster\.   | 
|  Cross\-realm trust principal password \(optional\)  |  Required when establishing a cross\-realm trust\. The cross\-realm principal password, which must be identical across realms\. Use a strong password\.  | 
|  AD domain join user \(optional\)  |  Required when establishing a cross\-realm trust with an Active Directory domain\. This is User logon name of an Active Directory account with sufficient privileges to join computers to the domain\. Amazon EMR uses this identity to join the cluster to the domain\. For more information, see [Step 3: Add User Accounts to the Domain for the EMR Cluster](emr-kerberos-cross-realm.md#emr-kerberos-ad-users)\.  | 
|  AD domain join password \(optional\)  |  The password for the Active Directory user that has sufficient privileges to join the cluster to the Active Directory domain\. For more information, see [Step 3: Add User Accounts to the Domain for the EMR Cluster](emr-kerberos-cross-realm.md#emr-kerberos-ad-users)\.  | 

The following examples demonstrate the same configurations specified in the Amazon EMR console and using the `create-cluster` command from the AWS CLI\. 

**Example Example Console Configuration**  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-clstr-cfg.png)

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

**Example Example `create-cluster` Command**  

```
aws emr create-cluster --name "MyKerberosCluster" \
--release-label emr-5.10.0 \
--instance-type m3.xlarge \
--instance-count 3 \
--use-default-roles \
--ec2-attributes KeyName=MyEC2KeyPair \
--security-configuration KerberosSecurityConfig \
--applications Name=Hadoop Name=Hive Name=Oozie Name=Hue Name=HCatalog Name=Spark \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyVeryStrongPassword,\
CrossRealmTrustPrincipalPassword=MyVeryStrongMatchingPassword,\
ADDomainJoinUser=ADUser,ADDomainJoinPassword=MyADUserPassword
```

## Step 3: Add Kerberos\-Authenticated Users<a name="emr-kerberos-configuration-users"></a>

Amazon EMR creates Kerberos\-authenticated clients for applications that run on the cluster, for example, the `Hadoop` user, `Spark` user, and others\. You can also add users who are authenticated to cluster processes using Kerberos\. Authenticated users can connect to the cluster with their Kerberos credentials\.

You can add users in either of the following ways:
+ Configure a cross\-realm trust to authenticate users from a different Kerberos realm, such as an Active Directory directory\. For more information, see [Configure a Cross\-Realm Trust](emr-kerberos-cross-realm.md)\.
+ Add Linux accounts on the local cluster and add principals to the cluster\-dedicated KDC for those accounts\. For more information, see [Configure a Cluster\-Dedicated KDC](emr-kerberos-cluster-kdc.md)\.
**Important**  
The KDC, along with the database of principals, is lost when the master node terminates because the master node uses ephemeral storage\. If you create users for SSH connections, we recommend that you establish a cross\-realm trust with an external KDC configured for high\-availability\. Alternatively, if you create users for SSH connections using Linux user accounts, automate the account creation process using bootstrap actions and scripts so that it can be repeated when you create a new cluster\.