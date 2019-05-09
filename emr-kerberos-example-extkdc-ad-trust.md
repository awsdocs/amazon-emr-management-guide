# External Cluster KDC with Active Directory Cross\-Realm Trust<a name="emr-kerberos-example-extkdc-ad-trust"></a>

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
aws emr create-cluster --release-label emr-5.23.0 \
--instance-count 3 --instance-type m4.large --applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration ExtKDCWithADIntegration \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=KDCOnMasterPassword,\
ADDomainJoinUser=MyPrivilegedADUserName,ADDomainJoinPassword=PasswordForADDomainJoinUser
```