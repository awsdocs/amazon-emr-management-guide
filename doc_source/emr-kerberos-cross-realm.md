# Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain<a name="emr-kerberos-cross-realm"></a>

When you set up a cross\-realm trust, you allow principals \(usually users\) from a different Kerberos realm to authenticate to application components on the EMR cluster\. The cluster\-dedicated KDC establishes a trust relationship with another KDC using a *cross\-realm principal* that exists in both KDCs\. The principal name and the password match precisely\.

A cross\-realm trust requires that the KDCs can reach one another over the network and resolve each other's domain names\. Steps for establishing a cross\-realm trust relationship with a Microsoft AD domain controller running as an EC2 instance are provided below, along with an example network setup that provides the required connectivity and domain\-name resolution\. Any network setup that allows the required network traffic between KDCs is acceptable\.

Optionally, after you establish a cross\-realm trust with Active Directory using a KDC on one cluster, you can create another cluster using a different security configuration to reference the KDC on the first cluster as an external KDC\. For an example security configuration and cluster set up, see [External Cluster KDC with Active Directory Cross\-Realm Trust](emr-kerberos-config-examples.md#emr-kerberos-example-extkdc-ad-trust)\.

**Important**  
Amazon EMR does not support cross\-realm trusts with AWS Directory Service for Microsoft Active Directory\.

[Step 1: Set Up the VPC and Subnet](#emr-kerberos-ad-network)

[Step 2: Launch and Install the Active Directory Domain Controller](#emr-kerberos-ad-dc)

[Step 3: Add User Accounts to the Domain for the EMR Cluster](#emr-kerberos-ad-users)

[Step 4: Configure an Incoming Trust on the Active Directory Domain Controller](#emr-kerberos-ad-configure-trust)

[Step 5: Use a DHCP Option Set to Specify the Active Directory Domain Controller as a VPC DNS Server](#emr-kerberos-ad-DHCP)

[Step 6: Launch a Kerberized EMR Cluster](#emr-kerberos-ad-cluster)

[Step 7: Create HDFS Users and Set Permissions on the Cluster for Active Directory User Accounts](#emr-kerberos-ad-hadoopuser)

## Step 1: Set Up the VPC and Subnet<a name="emr-kerberos-ad-network"></a>

The following steps demonstrate creating a VPC and subnet so that the cluster\-dedicated KDC can reach the Active Directory domain controller and resolve its domain name\. In these steps, domain\-name resolution is provided by referencing the Active Directory domain controller as the domain name server in the DHCP option set\. For more information, see [Step 5: Use a DHCP Option Set to Specify the Active Directory Domain Controller as a VPC DNS Server](#emr-kerberos-ad-DHCP)\.

The KDC and the Active Directory domain controller must be able to resolve one other's domain names\. This allows Amazon EMR to join computers to the domain and automatically configure corresponding Linux user accounts and SSH parameters on cluster instances\. 

If Amazon EMR can't resolve the domain name, you can reference the trust using the Active Directory domain controller's IP address\. However, you must manually add Linux user accounts, add corresponding principals to the cluster\-dedicated KDC, and configure SSH\.

**To set up the VPC and subnet**

1. Create an Amazon VPC with a single public subnet\. For more information, see [Step 1: Create the VPC](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/getting-started-ipv4.html#getting-started-create-vpc) in the *Amazon VPC Getting Started Guide*\.
**Important**  
When you use a Microsoft Active Directory domain controller, choose a CIDR block for the EMR cluster so that all IPv4 addresses are fewer than nine characters in length \(for example, 10\.0\.0\.0/16\)\. This is because the DNS names of cluster computers are used when the computers join the Active Directory directory\. AWS assigns [DNS Hostnames](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-hostnames) based on IPv4 address in a way that longer IP addresses may result in DNS names longer than 15 characters\. Active Directory has a 15\-character limit for registering joined computer names, and truncates longer names, which can cause unpredictable errors\.

1. Remove the default DHCP option set assigned to the VPC\. For more information, see [Changing a VPC to use No DHCP Options](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html#DHCP_Use_No_Options)\. Later on, you add a new one that specifies the Active Directory domain controller as the DNS server\. 

1. Confirm that DNS support is enabled for the VPC, that is, that DNS Hostnames and DNS Resolution are both enabled\. They are enabled by default\. For more information, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-updating)\.

1. Confirm that your VPC has an internet gateway attached, which is the default\. For more information, see [Creating and Attaching an Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway)\.
**Note**  
An internet gateway is used in this example because you are establishing a new domain controller for the VPC\. An internet gateway may not be required for your application\. The only requirement is that the cluster\-dedicated KDC can access the Active Directory domain controller\.

1. Create a custom route table, add a route that targets the Internet Gateway, and then attach it to your subnet\. For more information, see [Create a Custom Route Table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing)\.

1. When you launch the EC2 instance for the domain controller, it must have a static public IPv4 address for you to connect to it using RDP\. The easiest way to do this is to configure your subnet to auto\-assign public IPv4 addresses\. This is not the default setting when a subnet is created\. For more information, see [Modifying the Public IPv4 Addressing Attribute of your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip)\. Optionally, you can assign the address when you launch the instance\. For more information, see [Assigning a Public IPv4 Address During Instance Launch](https://docs.aws.amazon.com/vpc/latest/userguide/using-instance-addressing.html#public-ip-addresses)\.

1. When you finish, make a note of your VPC and subnet IDs\. You use them later when you launch the Active Directory domain controller and the cluster\.

## Step 2: Launch and Install the Active Directory Domain Controller<a name="emr-kerberos-ad-dc"></a>

1. Launch an EC2 instance based on the Microsoft Windows Server 2016 Base AMI\. We recommend an m4\.xlarge or better instance type\. For more information, see [Launching an AWS Marketplace Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/launch-marketplace-console.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Make a note of the Group ID of the security group associated with the EC2 instance\. You need it for [Step 6: Launch a Kerberized EMR Cluster](#emr-kerberos-ad-cluster)\. We use *sg\-012xrlmdomain345*\. Alternatively, you can specify different security groups for the EMR cluster and this instance that allows traffic between them\. For more information, see [Amazon EC2 Security Groups for Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to the EC2 instance using RDP\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Start **Server Manager** to install and configure the Active Directory Domain Services role on the server\. Promote the server to a domain controller and assign a domain name \(the example we use here is `ad.domain.com`\)\. Make a note of the domain name because you need it later when you create the EMR security configuration and cluster\. If you are new to setting up Active Directory, you can follow the instructions in [How to Set Up Active Directory \(AD\) in Windows Server 2016](https://ittutorials.net/microsoft/windows-server-2016/setting-up-active-directory-ad-in-windows-server-2016/)\.

   The instance restarts when you finish\.

## Step 3: Add User Accounts to the Domain for the EMR Cluster<a name="emr-kerberos-ad-users"></a>

RDP to the Active Directory domain controller to create user accounts in Active Directory Users and Computers for each cluster user\. For instructions, see [Create a User Account in Active Directory Users and Computers](https://technet.microsoft.com/en-us/library/dd894463(v=ws.10).aspx)\. Make a note of each user's **User logon name**\. You need these later when you configure the cluster\. 

In addition, create a user account with sufficient privileges to join computers to the domain\. You specify this account when you create a cluster\. Amazon EMR uses it to join cluster instances to the domain\. You specify this account and its password in [Step 6: Launch a Kerberized EMR Cluster](#emr-kerberos-ad-cluster)\. To delegate computer join privileges to the user account, we recommend that you create a group with join privileges and then assign the user to the group\. For instructions, see [Delegating Directory Join Privileges](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_join_privileges.html) in the *AWS Directory Service Administration Guide*\.

## Step 4: Configure an Incoming Trust on the Active Directory Domain Controller<a name="emr-kerberos-ad-configure-trust"></a>

The example commands below create a trust in Active Directory, which is a one\-way, incoming, non\-transitive, realm trust with the cluster\-dedicated KDC\. The example we use for the cluster's realm is `EC2.INTERNAL`\. Replace the *KDC\-FQDN* with the **Public DNS** name listed for the Amazon EMR master node hosting the KDC\. The `passwordt` parameter specifies the **cross\-realm principal password**, which you specify along with the cluster **realm** when you create a cluster\. The realm name is derived from the default domain name in `us-east-1` for the cluster\. The `Domain` is the Active Directory domain in which you are creating the trust, which is lower case by convention\. The example uses `ad.domain.com`

Open the Windows command prompt with administrator privileges and type the following commands to create the trust relationship on the Active Directory domain controller:

```
C:\Users\Administrator> ksetup /addkdc EC2.INTERNAL KDC-FQDN
C:\Users\Administrator> netdom trust EC2.INTERNAL /Domain:ad.domain.com /add /realm /passwordt:MyVeryStrongPassword
C:\Users\Administrator> ksetup /SetEncTypeAttr EC2.INTERNAL AES256-CTS-HMAC-SHA1-96
```

## Step 5: Use a DHCP Option Set to Specify the Active Directory Domain Controller as a VPC DNS Server<a name="emr-kerberos-ad-DHCP"></a>

Now that the Active Directory domain controller is configured, you must configure the VPC to use it as a domain name server for name resolution within your VPC\. To do this, attach a DHCP options set\. Specify the **Domain name** as the domain name of your cluster—for example, `ec2.internal` if your cluster is in us\-east\-1 or `region.compute.internal` for other regions\. For **Domain name servers**, you must specify the IP address of the Active Directory domain controller \(which must be reachable from the cluster\) as the first entry, followed by **AmazonProvidedDNS** \(for example, ***xx\.xx\.xx\.xx*,AmazonProvidedDNS**\)\. For more information, see [Changing DHCP Option Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html#DHCPOptions)\.

## Step 6: Launch a Kerberized EMR Cluster<a name="emr-kerberos-ad-cluster"></a>

1. In Amazon EMR, create a security configuration that specifies the Active Directory domain controller you created in the previous steps\. An example command is shown below\. Replace the domain, `ad.domain.com`, with the name of the domain you specified in [Step 2: Launch and Install the Active Directory Domain Controller](#emr-kerberos-ad-dc)\.

   ```
   aws emr create-security-configuration --name MyKerberosConfig \
   --security-configuration '{
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
   }'
   ```

1. Create the cluster with the following attributes:
   + Use the `--security-configuration` option to specify the security configuration that you created\. We use *MyKerberosConfig* in the example\.
   + Use the `SubnetId` property of the `--ec2-attributes option` to specify the subnet that you created in [Step 1: Set Up the VPC and Subnet](#emr-kerberos-ad-network)\. We use *step1\-subnet* in the example\.
   + Use the `AdditionalMasterSecurityGroups` and `AdditionalSlaveSecurityGroups` of the `--ec2-attributes` option to specify that the security group associated with the AD Domain Controller from [Step 2: Launch and Install the Active Directory Domain Controller](#emr-kerberos-ad-dc) is associated with the cluster master node as well as core and task nodes\. We use *sg\-012xrlmdomain345* in the example\.

   Use `--kerberos-attributes` to specify the following cluster\-specific Kerberos attributes:
   + The realm for the cluster that you specified when you set up the Active Directory domain controller\.
   + The cross\-realm trust principal password that you specified as `passwordt` in [Step 4: Configure an Incoming Trust on the Active Directory Domain Controller](#emr-kerberos-ad-configure-trust)\.
   + A `KdcAdminPassword`, which you can use to administer the cluster\-dedicated KDC\.
   + The user logon name and password of the Active Directory account with computer join privileges that you created in [Step 3: Add User Accounts to the Domain for the EMR Cluster](#emr-kerberos-ad-users)\.

   The following example launches a kerberized cluster\.

   ```
   aws emr create-cluster --name "MyKerberosCluster" \
   --release-label emr-5.10.0 \
   --instance-type m5.xlarge \
   --instance-count 3 \
   --ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2KeyPair,\
   SubnetId=step1-subnet, AdditionalMasterSecurityGroups=sg-012xrlmdomain345,
   AdditionalSlaveSecurityGroups=sg-012xrlmdomain345\
   --service-role EMR_DefaultRole \
   --security-configuration MyKerberosConfig \
   --applications Name=Hadoop Name=Hive Name=Oozie Name=Hue Name=HCatalog Name=Spark \
   --kerberos-attributes Realm=EC2.INTERNAL,\
   KdcAdminPassword=MyClusterKDCAdminPwd,\
   ADDomainJoinUser=ADUserLogonName,ADDomainJoinPassword=ADUserPassword,\
   CrossRealmTrustPrincipalPassword=MatchADTrustPwd
   ```

## Step 7: Create HDFS Users and Set Permissions on the Cluster for Active Directory User Accounts<a name="emr-kerberos-ad-hadoopuser"></a>

When setting up a trust relationship with Active Directory, Amazon EMR creates Linux users on the cluster for each Active Directory user account\. For example, the user logon name `LiJuan` in Active Directory has a Linux user account of `lijuan`\. Active Directory user names can contain upper\-case letters, but Linux does not honor Active Directory casing\.

To allow your users to log in to the cluster to run Hadoop jobs, you must add HDFS user directories for their Linux user accounts, and grant each user ownership of their directory\. To do this, we recommend that you run a script saved to Amazon S3 as a cluster step\. Alternatively, you can run the commands in the script below from the command line on the master node\. Use the EC2 key pair that you specified when you created the cluster to connect to the master node over SSH as the Hadoop user\. For more information, see [Use an Amazon EC2 Key Pair for SSH Credentials](emr-plan-access-ssh.md)\.

Run the following command to add a step to the cluster that runs a script, *AddHDFSUsers\.sh*\.

```
aws emr add-steps --cluster-id ClusterID \
--steps Type=CUSTOM_JAR,Name=CustomJAR,ActionOnFailure=CONTINUE,\
Jar=s3://MyRegion.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://MyBucketPath/AddHDFSUsers.sh"]
```

The contents of the file *AddHDFSUsers\.sh* is as follows\.

```
#!/bin/bash
# AddHDFSUsers.sh script

# Initialize an array of user names from AD or Linux users and KDC principals created manually on the cluster
ADUSERS=("lijuan" "marymajor" "richardroe" "myusername")

# For each user listed, create an HDFS user directory
# and change ownership to the user

for username in ${ADUSERS[@]}; do
     hdfs dfs -mkdir /user/$username
     hdfs dfs -chown $username:$username /user/$username
done
```

### Active Directory Groups Mapped to Hadoop Groups<a name="emr-kerberos-ad-group"></a>

Amazon EMR uses System Security Services Daemon \(SSD\) to map Active Directory groups to Hadoop groups\. To confirm group mappings, after you log in to the master node as described in [Using SSH to Connect to Kerberized Clusters](emr-kerberos-connect-ssh.md), you can use the `hdfs groups` command to confirm that Active Directory groups to which your Active Directory account belongs have been mapped to Hadoop groups for the corresponding Hadoop user on the cluster\. You can also check other users' group mappings by specifying one or more user names with the command, for example `hdfs groups lijuan`\. For more information, see [groups](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#groups) in the [Apache HDFS Commands Guide](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)\.