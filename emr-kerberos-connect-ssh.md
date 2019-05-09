# Using SSH to Connect to Kerberized Clusters<a name="emr-kerberos-connect-ssh"></a>

This section demonstrates the steps for a Kerberos\-authenticated user to connect to the master node of an EMR cluster\.

Each computer that is used for an SSH connection must have SSH client and Kerberos client applications installed\. Linux computers most likely include these by default\. For example, OpenSSH is installed on most Linux, Unix, and macOS operating systems\. You can check for an SSH client by typing ssh at the command line\. If your computer does not recognize the command, install an SSH client to connect to the master node\. The OpenSSH project provides a free implementation of the full suite of SSH tools\. For more information, see the [OpenSSH](http://www.openssh.org/) website\. Windows users can use applications such as [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) as an SSH client\. 

For more information about SSH connections, see [Connect to the Cluster](emr-connect-master-node.md)\.

SSH uses GSSAPI for authenticating Kerberos clients, and you must enable GSSAPI authentication for the SSH service on the cluster master node\. For more information, see [Enabling GSSAPI for SSH](emr-kerberos-configuration-users.md#emr-kerberos-ssh-config)\. SSH clients must also use GSSAPI\.

In the following examples, for *MasterPublicDNS* use the value that appears for **Master public DNS** on the **Summary** tab of the cluster details pane—for example, *ec2\-11\-222\-33\-44\.compute\-1\.amazonaws\.com*\.

## Prerequisite for krb5\.conf \(Non Active Directory\)<a name="emr-kerberos-conffile"></a>

When using a configuration without Active Directory integration, in addition to the SSH client and Kerberos client applications, each client computer must have a copy of the `/etc/krb5.conf` file that matches the `/etc/krb5.conf` file on the cluster master node\.

**To copy the krb5\.conf file**

1. Use SSH to connect to the master node using an EC2 key pair and the default `hadoop` user—for example, `hadoop@MasterPublicDNS`\. For detailed instructions, see [Connect to the Cluster](emr-connect-master-node.md)\.

1. From the master node, copy the contents of the `/etc/krb5.conf` file \. For more information, see [Connect to the Cluster](emr-connect-master-node.md)\.

1. On each client computer that will connect to the cluster, create an identical `/etc/krb5.conf` file based on the copy that you made in the previous step\.

## Using Kinit and SSH<a name="emr-kerberos-kinit-ssh"></a>

Each time a user connects from a client computer using Kerberos credentials, the user must first renew Kerberos tickets for their user on the client computer\. In addition, the SSH client must be configured to use GSSAPI authentication\.

**To use SSH to connect to a Kerberized EMR cluster**

1. Use `kinit` to renew your Kerberos tickets as shown in the following example

   ```
   kinit user1
   ```

1. Use an `ssh` client along with the principal that you created in the cluster\-dedicated KDC or Active Directory user name\. Make sure that GSSAPI authentication is enabled as shown in the following examples\.

   **Example: Linux users**

   The `-K `option specifies GSSAPI authentication\.

   ```
   ssh -K user1@MasterPublicDNS
   ```

   **Example: Windows users \(PuTTY\)**

   Make sure that the GSSAPI authentication option for the session is enabled as shown:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-gssapi-putty.png)