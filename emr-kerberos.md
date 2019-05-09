# Use Kerberos Authentication<a name="emr-kerberos"></a>

Amazon EMR release version 5\.10\.0 and later supports Kerberos, which is a network authentication protocol created by the Massachusetts Institute of Technology \(MIT\)\. Kerberos uses secret\-key cryptography to provide strong authentication so that passwords or other credentials aren't sent over the network in an unencrypted format\.

In Kerberos, services and users that need to authenticate are known as *principals*\. Principals exist within a Kerberos *realm*\. Within the realm, a Kerberos server known as the *key distribution center \(KDC\)* provides the means for principals to authenticate\. The KDC does this by issuing *tickets* for authentication\. The KDC maintains a database of the principals within its realm, their passwords, and other administrative information about each principal\. A KDC can also accept authentication credentials from principals in other realms, which is known as a *cross\-realm trust*\. In addition, an EMR cluster can use an external KDC to authenticate principals\.

A common scenario for establishing a cross\-realm trust or using an external KDC is to authenticate users from an Active Directory domain\. This allows users to access an EMR cluster with their domain user account when they use SSH to connect to a cluster or work with big data applications\.

When you use Kerberos authentication, Amazon EMR configures Kerberos for the applications, components, and subsystems that it installs on the cluster so that they are authenticated with each other\.

**Important**  
Amazon EMR does not support AWS Directory Service for Microsoft Active Directory in a cross\-realm trust or as an external KDC\.

Before you configure Kerberos using Amazon EMR, we recommend that you become familiar with Kerberos concepts, the services that run on a KDC, and the tools for administering Kerberos services\. For more information, see [MIT Kerberos Documentation](http://web.mit.edu/kerberos/krb5-latest/doc/), which is published by the [Kerberos Consortium](http://kerberos.org/)\.

**Topics**
+ [Supported Applications](emr-kerberos-principals.md)
+ [Kerberos Architecture Options](emr-kerberos-options.md)
+ [Configuring Kerberos on Amazon EMR](emr-kerberos-configure.md)
+ [Using SSH to Connect to Kerberized Clusters](emr-kerberos-connect-ssh.md)
+ [Tutorial: Configure a Cluster\-Dedicated KDC](emr-kerberos-cluster-kdc.md)
+ [Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain](emr-kerberos-cross-realm.md)