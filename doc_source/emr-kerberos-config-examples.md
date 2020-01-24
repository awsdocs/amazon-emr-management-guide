# Configuration Examples<a name="emr-kerberos-config-examples"></a>

The following examples demonstrate security configurations and cluster configurations for common scenarios\. AWS CLI commands are shown for brevity\.

## Local KDC<a name="emr-kerberos-example-local-kdc"></a>

The following commands create a cluster with a cluster\-dedicated KDC running on the master node\. Additional configuration on the cluster is required\. For more information, see [Configuring a Cluster for Kerberos\-Authenticated HDFS Users and SSH Connections](emr-kerberos-configuration-users.md)\.

**Create Security Configuration**

```
aws emr create-security-configuration --name LocalKDCSecurityConfig \
--security-configuration '{"AuthenticationConfiguration": \
{"KerberosConfiguration": {"Provider": "ClusterDedicatedKdc",\
"ClusterDedicatedKdcConfiguration": {"TicketLifetimeInHours": 24 }}}}'
```

**Create Cluster**

```
aws emr create-cluster --release-label emr-5.29.0 \
--instance-count 3 --instance-type m5.xlarge \
--applications Name=Hadoop Name=Hive --ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole \
--security-configuration LocalKDCSecurityConfig \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyPassword
```

## Cluster\-Dedicated KDC with Active Directory Cross\-Realm Trust<a name="emr-kerberos-example-crossrealm"></a>

The following commands create a cluster with a cluster\-dedicated KDC running on the master node with a cross\-realm trust to an Active Directory domain\. Additional configuration on the cluster and in Active Directory is required\. For more information, see [Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain](emr-kerberos-cross-realm.md)\.

**Create Security Configuration**

```
aws emr create-security-configuration --name LocalKDCWithADTrustSecurityConfig \
--security-configuration '{"AuthenticationConfiguration": \
{"KerberosConfiguration": {"Provider": "ClusterDedicatedKdc", \
"ClusterDedicatedKdcConfiguration": {"TicketLifetimeInHours": 24, \
"CrossRealmTrustConfiguration": {"Realm":"AD.DOMAIN.COM", \
"Domain":"ad.domain.com", "AdminServer":"ad.domain.com", \
"KdcServer":"ad.domain.com"}}}}}'
```

**Create Cluster**

```
aws emr create-cluster --release-label emr-5.29.0 \
--instance-count 3 --instance-type m5.xlarge --applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration KDCWithADTrustSecurityConfig \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyClusterKDCAdminPassword,\
ADDomainJoinUser=ADUserLogonName,ADDomainJoinPassword=ADUserPassword,\
CrossRealmTrustPrincipalPassword=MatchADTrustPassword
```

## External KDC on a Different Cluster<a name="emr-kerberos-example-extkdc-cluster"></a>

The following commands create a cluster that references a cluster\-dedicated KDC on the master node of a different cluster to authenticate principals\. Additional configuration on the cluster is required\. For more information, see [Configuring a Cluster for Kerberos\-Authenticated HDFS Users and SSH Connections](emr-kerberos-configuration-users.md)\.

**Create Security Configuration**

```
aws emr create-security-configuration --name ExtKDCOnDifferentCluster \
--security-configuration '{"AuthenticationConfiguration": \
{"KerberosConfiguration": {"Provider": "ExternalKdc", \
"ExternalKdcConfiguration": {"KdcServerType": "Single", \
"AdminServer": "MasterDNSOfKDCMaster:749", \
"KdcServer": "MasterDNSOfKDCMaster:88"}}}}'
```

**Create Cluster**

```
aws emr create-cluster --release-label emr-5.29.0 \
--instance-count 3 --instance-type m5.xlarge \
--applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration ExtKDCOnDifferentCluster \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=KDCOnMasterPassword
```

## External Cluster KDC with Active Directory Cross\-Realm Trust<a name="emr-kerberos-example-extkdc-ad-trust"></a>

The following commands create a cluster with no KDC\. The cluster references a cluster\-dedicated KDC running on the master node of another cluster to authenticate principals\. That KDC has a cross\-realm trust with an Active Directory domain controller\. Additional configuration on the master node with the KDC is required\. For more information, see [Tutorial: Configure a Cross\-Realm Trust with an Active Directory Domain](emr-kerberos-cross-realm.md)\.

**Create Security Configuration**

```
aws emr create-security-configuration --name ExtKDCWithADIntegration \
--security-configuration '{"AuthenticationConfiguration": \
{"KerberosConfiguration": {"Provider": "ExternalKdc", \
"ExternalKdcConfiguration": {"KdcServerType": "Single", \
"AdminServer": "MasterDNSofClusterKDC:749", \
"KdcServer": "MasterDNSofClusterKDC.com:88", \
"AdIntegrationConfiguration": {"AdRealm":"AD.DOMAIN.COM", \
"AdDomain":"ad.domain.com"}}}}}'
```

**Create Cluster**

```
aws emr create-cluster --release-label emr-5.29.0 \
--instance-count 3 --instance-type m5.xlarge --applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration ExtKDCWithADIntegration \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=KDCOnMasterPassword,\
ADDomainJoinUser=MyPrivilegedADUserName,ADDomainJoinPassword=PasswordForADDomainJoinUser
```