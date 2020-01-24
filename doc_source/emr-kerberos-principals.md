# Supported Applications<a name="emr-kerberos-principals"></a>

Within an EMR cluster, Kerberos principals are the big data application services and subsystems that run on all cluster nodes\. Amazon EMR can configure the applications and components listed below to use Kerberos\. Each application has a Kerberos user principal associated with it\.

Amazon EMR does not support cross\-realm trusts with AWS Directory Service for Microsoft Active Directory\.

Amazon EMR only configures the open\-source Kerberos authentication features for the applications and components listed below\. Any other applications installed are not Kerberized, which can result in an inability to communicate with Kerberized components and cause application errors\. Applications and components that are not Kerberized do not have authentication enabled\. Supported applications and components may vary by Amazon EMR release version\.

No web user interfaces hosted on the cluster are Kerberized\.
+ **Hadoop MapReduce**
+ **Hbase**
+ **HCatalog**
+ **HDFS**
+ **Hive**
  + Do not enable Hive with LDAP authentication\. This may cause issues communicating with Kerberized YARN\.
+ **Hue**
  + Hue user authentication isn't set automatically and can be configured using the configuration API\.
  + Hue server is Kerberized\. The Hue front\-end \(UI\) is not configured for authentication\. LDAP authentication can be configured for the Hue UI\. 
+ **Livy**
+ **Oozie**
+ **Phoenix**
+ **Spark**
+ **Tez**
+ **YARN**
+ **Zeppelin**
  + Zeppelin is only configured to use Kerberos with the Spark interpreter\. It is not configured for other interpreters\.
  + Zeppelin impersonation with Kerberos is not supported\. All users logged in to Zeppelin use the same Zeppelin user principal to run Spark jobs and authenticate to YARN\.
+ **Zookeeper**
  + Zookeeper client is not supported\.