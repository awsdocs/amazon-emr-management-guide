# Local KDC<a name="emr-kerberos-example-local-kdc"></a>

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
aws emr create-cluster --release-label emr-5.23.0 \
--instance-count 3 --instance-type m4.large \
--applications Name=Hadoop Name=Hive --ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2Key \
--service-role EMR_DefaultRole \
--security-configuration LocalKDCSecurityConfig \
--kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyPassword
```