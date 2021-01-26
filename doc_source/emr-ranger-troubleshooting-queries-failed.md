# Queries are unexpectedly failing<a name="emr-ranger-troubleshooting-queries-failed"></a>

**Check Apache Ranger plugin logs \(Apache Hive, EMR RecordServer, EMR SecretAgent, etc\., logs\)**

This section is common across all applications that integrate with the Ranger Plugin, such as Apache Hive, EMR Record Server, and EMR SecretAgent\.

**Common Error Messages**


| Error Message | Cause | 
| --- | --- | 
|  ERROR PolicyRefresher:272 \- \[\] PolicyRefresher\(serviceName=policy\-repository\): failed to find service\. Will clean up local cache of policies \(\-1\)   |  This error messages means that the service name you provided in the EMR security configuration does not match a service policy repository in the Ranger Admin server\.  | 

If within Ranger Admin server your AMAZON\-EMR\-SPARK service looks like the following, then you should enter **amazonemrspark** as the service name\.

![\[Ranger Admin server showing AMAZON-EMR-SPARK troubleshooting.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-amazon-emr-spark-troubleshooting.png)