# Cluster\-Dedicated KDC with Active Directory Cross\-Realm Trust<a name="emr-kerberos-example-crossrealm"></a>

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
aws emr create-cluster --release-label emr-5.23.0 \
--instance-count 3 --instance-type m4.large --applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration KDCWithADTrustSecurityConfig \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyClusterKDCAdminPassword,\
ADDomainJoinUser=ADUserLogonName,ADDomainJoinPassword=ADUserPassword,\
CrossRealmTrustPrincipalPassword=MatchADTrustPassword
```