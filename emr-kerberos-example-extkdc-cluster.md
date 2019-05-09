# External KDC on a Different Cluster<a name="emr-kerberos-example-extkdc-cluster"></a>

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
aws emr create-cluster --release-label emr-5.23.0 \
--instance-count 3 --instance-type m4.large \
--applications Name=Hadoop Name=Hive \
--ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole --security-configuration ExtKDCOnDifferentCluster \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=KDCOnMasterPassword
```