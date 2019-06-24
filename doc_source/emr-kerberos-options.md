# Kerberos Architecture Options<a name="emr-kerberos-options"></a>

When you use Kerberos with Amazon EMR, you can choose from the architectures listed in this section\. Regardless of the architecture that you choose, you configure Kerberos using the same steps\. You create a security configuration, you specify the security configuration and compatible cluster\-specific Kerberos options when you create the cluster, and you create HDFS directories for Linux users on the cluster that match user principals in the KDC\. For an explanation of configuration options and example configurations for each architecture, see [Configuring Kerberos on Amazon EMR](emr-kerberos-configure.md)\.

## Cluster\-Dedicated KDC \(KDC on Master Node\)<a name="emr-kerberos-localkdc-summary"></a>

This configuration is available with Amazon EMR release version 5\.10\.0 and later\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-cluster-dedicated-kdc.png)

**Advantages**
+ Amazon EMR has full ownership of the KDC\.
+ The KDC on the EMR cluster is independent from centralized KDC implementations such as Microsoft Active Directory or AWS Managed Microsoft AD\.
+ Performance impact is minimal because the KDC manages authentication only for local nodes within the cluster\.
+ Optionally, other Kerberized clusters can reference the KDC as an external KDC\. For more information, see [External KDC—Master Node on a Different Cluster](#emr-kerberos-extkdc-cluster-summary)\.

**Considerations and Limitations**
+ Kerberized clusters can not authenticate to one another, so applications can not interoperate\. If cluster applications need to interoperate, you must establish a cross\-realm trust between clusters, or set up one cluster as the external KDC for other clusters\. If a cross\-realm trust is established, the KDCs must have different Kerberos realms\.
+ You must create Linux users on the EC2 instance of the master node that correspond to KDC user principals, along with the HDFS directories for each user\.
+ User principals must use an EC2 private key file and `kinit` credentials to connect to the cluster using SSH\.

## Cross\-Realm Trust<a name="emr-kerberos-crossrealm-summary"></a>

In this configuration, principals \(usually users\) from a different Kerberos realm authenticate to application components on a Kerberized EMR cluster, which has its own KDC\. The KDC on the master node establishes a trust relationship with another KDC using a *cross\-realm principal* that exists in both KDCs\. The principal name and the password match precisely in each KDC\. Cross\-realm trusts are most common with Active Directory implementations, as shown in the following diagram\. Cross\-realm trusts with an external MIT KDC or a KDC on another Amazon EMR cluster are also supported\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-cross-realm-trust.png)

**Advantages**
+ The EMR cluster on which the KDC is installed maintains full ownership of the KDC\.
+ With Active Directory, Amazon EMR automatically creates Linux users that correspond to user principals from the KDC\. You still must create HDFS directories for each user\. In addition, user principals in the Active Directory domain can access Kerberized clusters using `kinit` credentials, without the EC2 private key file\. This eliminates the need to share the private key file among cluster users\.
+ Because each cluster KDC manages authentication for the nodes in the cluster, the effects of network latency and processing overhead for a large number of nodes across clusters is minimized\.

**Considerations and Limitations**
+ If you are establishing a trust with an Active Directory realm, you must provide an Active Directory user name and password with permissions to join principals to the domain when you create the cluster\.
+ Cross\-realm trusts cannot be established between Kerberos realms with the same name\.
+ Cross\-realm trusts must be established explicitly\. For example, if Cluster A and Cluster B both establish a cross\-realm trust with a KDC, they do not inherently trust one another and their applications cannot authenticate to one another to interoperate\.
+ KDCs must be maintained independently and coordinated so that credentials of user principals match precisely\.

## External KDC<a name="emr-kerberos-extkdc-summary"></a>

Configurations with an External KDC are supported with Amazon EMR 5\.20\.0 and later\.
+ [External KDC—MIT KDC](#emr-kerberos-extkdc-mit-summary)
+ [External KDC—Master Node on a Different Cluster](#emr-kerberos-extkdc-cluster-summary)
+ [External KDC—Cluster KDC on a Different Cluster with Active Directory Cross\-Realm Trust](#emr-kerberos-extkdc-ad-trust-summary)

### External KDC—MIT KDC<a name="emr-kerberos-extkdc-mit-summary"></a>

This configuration allows one or more EMR clusters to use principals defined and maintained in an MIT KDC server\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-external-kdc.png)

**Advantages**
+ Managing principals is consolidated in a single KDC\.
+ Multiple clusters can use the same KDC in the same Kerberos realm\. This allows cluster applications to interoperate and simplifies the authentication of communication between clusters as compared to a cross\-realm trust\.
+ The master node on a Kerberized cluster does not have the performance burden associated with maintaining the KDC\.

**Considerations and Limitations**
+ You must create Linux users on the EC2 instance of each Kerberized cluster's master node that correspond to KDC user principals, along with the HDFS directories for each user\.
+ User principals must use an EC2 private key file and `kinit` credentials to connect to Kerberized clusters using SSH\.
+ Each node in Kerberized EMR clusters must have a network route to the KDC\.
+ Each node in Kerberized clusters places an authentication burden on the external KDC, so the configuration of the KDC affects cluster performance\. When you configure the hardware of the KDC server, consider the maximum number of Amazon EMR nodes to be supported simultaneously\.
+ Cluster performance is dependent on the network latency between nodes in Kerberized clusters and the KDC\.
+ Troubleshooting can be more difficult because of interdependencies\.

### External KDC—Master Node on a Different Cluster<a name="emr-kerberos-extkdc-cluster-summary"></a>

This configuration is nearly identical to the external MIT KDC implementation above, except that the KDC is on the master node of an EMR cluster\. For more information, see [Cluster\-Dedicated KDC \(KDC on Master Node\)](#emr-kerberos-localkdc-summary) and [Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain](emr-kerberos-cross-realm.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-external-cluster-kdc.png)

**Advantages**
+ Managing principals is consolidated in a single KDC\.
+ Multiple clusters can use the same KDC in the same Kerberos realm\. This allows cluster applications an Kerberized clusters to interoperate\. It also simplifies the authentication of communication between clusters as compared to a cross\-realm trust\.

**Considerations and Limitations**
+ You must create Linux users on the EC2 instance of each Kerberized cluster's master node that correspond to KDC user principals, along with the HDFS directories for each user\.
+ User principals must use an EC2 private key file and `kinit` credentials to connect to Kerberized clusters using SSH\.
+ Each node in each EMR cluster must have a network route to the KDC\.
+ Each Amazon EMR node in Kerberized clusters places an authentication burden on the external KDC, so the configuration of the KDC affects cluster performance\. When you configure the hardware of the KDC server, consider the maximum number of Amazon EMR nodes to be supported simultaneously\.
+ Cluster performance is dependent on the network latency between nodes in the clusters and the KDC\.
+ Troubleshooting can be more difficult because of interdependencies\.

### External KDC—Cluster KDC on a Different Cluster with Active Directory Cross\-Realm Trust<a name="emr-kerberos-extkdc-ad-trust-summary"></a>

In this configuration, you first create a cluster with a cluster\-dedicated KDC that has a one\-way cross\-realm trust with Active Directory\. For a detailed tutorial, see [Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain](emr-kerberos-cross-realm.md)\. You then launch additional clusters, referencing the cluster KDC that has the trust as an external KDC\. For an example, see [External Cluster KDC with Active Directory Cross\-Realm Trust](emr-kerberos-example-extkdc-ad-trust.md)\. This allows each Amazon EMR cluster that uses the external KDC to authenticate principals defined and maintained in a Microsoft Active Directory domain\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/kerb-external-ad-trust-kdc.png)

**Advantages**
+ Managing principals is consolidated in the Active Directory domain\.
+ Amazon EMR joins the Active Directory realm, which eliminates the need to create Linux users that correspond Active Directory users\. You still must create HDFS directories for each user\.
+ Multiple clusters can use the same KDC in the same Kerberos realm, which is different from the Active Directory realm\. This allows cluster applications to interoperate\.
+ User principals in the Active Directory domain can access Kerberized clusters using `kinit` credentials, without the EC2 private key file\. This eliminates the need to share the private key file among cluster users\.
+ Only one Amazon EMR master node has the burden of maintaining the KDC, and only that cluster must be created with Active Directory credentials for the cross\-realm trust between the KDC and Active Directory\.

**Considerations and Limitations**
+ Each node in each EMR cluster must have a network route to the KDC and the Active Directory domain controller\.
+ Each Amazon EMR node places an authentication burden on the external KDC, so the configuration of the KDC affects cluster performance\. When you configure the hardware of the KDC server, consider the maximum number of Amazon EMR nodes to be supported simultaneously\.
+ Cluster performance is dependent on the network latency between nodes in the clusters and the KDC server\.
+ Troubleshooting can be more difficult because of interdependencies\.