# Use Kerberos Authentication<a name="emr-kerberos"></a>

Amazon EMR release version 5\.10\.0 and later supports Kerberos, which is a network authentication protocol created by the Massachusetts Institute of Technology \(MIT\)\. Kerberos uses secret\-key cryptography to provide strong authentication so that passwords or other credentials aren't sent over the network in an unencrypted format\.

In Kerberos, services and users that need to authenticate are known as *principals*\. Principals exist within a Kerberos *realm*\. Within the realm, a Kerberos server known as the *key distribution center \(KDC\)* provides the means for principals to authenticate\. The KDC does this by issuing *tickets* for authentication\. The KDC maintains a database of the principals within its realm, their passwords, and other administrative information about each principal\. A KDC can also accept authentication credentials from principals in other realms, which is known as a *cross\-realm trust*\. A common scenario for establishing a cross\-realm trust relationship is to authenticate users from an Active Directory domain so that they can access an EMR cluster using their domain user account, using SSH to connect to the cluster, or working with big data applications and viewing Web interfaces\.

Before you configure Kerberos using Amazon EMR, we recommend that you become familiar with Kerberos concepts, the services that run on a KDC, and the tools for administering Kerberos services\. For more information, see [MIT Kerberos Documentation](https://web.mit.edu/kerberos/krb5-latest/doc/), which is published by the [Kerberos Consortium](http://kerberos.org/)\.

When you create a Kerberized cluster, Amazon EMR creates and configures a cluster\-dedicated KDC that runs on the master node\. Amazon EMR configures Kerberos for the applications, components, and subsystems that it installs on the cluster so that they are authenticated with each other\. To set up users who authenticate using Kerberos, you can establish a cross\-realm trust to authenticate users from a different KDC, or you can add users manually to the cluster\-dedicated KDC\. You can then configure Hadoop user directories so that they can use their Kerberos credentials to run jobs and connect to the cluster\.

**Topics**
+ [Supported Applications](emr-kerberos-principals.md)
+ [Configure Kerberos](emr-kerberos-configure.md)
+ [Configure a Cluster\-Dedicated KDC](emr-kerberos-cluster-kdc.md)
+ [Configure a Cross\-Realm Trust](emr-kerberos-cross-realm.md)