# EMR Cluster failed to provision<a name="emr-ranger-troubleshooting-cluster-failed"></a>

There are several reasons why an EMR cluster may fail to start\. Here are a few ways to diagnose the issue\.

**Check EMR provisioning logs**

Amazon EMR uses Puppet to install and configure applications on an EMR cluster\. Looking at the logs will provide details as to if there are any errors during the provisioning phase of an EMR cluster\. The logs are accessible on cluster or S3 if logs are configured to be pushed to S3\.

The logs are stored in `/var/log/provision-node/apps-phase/0/{UUID}/puppet.log` on the disk and `s3://<LOG LOCATION>/<CLUSTER ID>/node/<EC2 INSTANCE ID>/provision-node/apps-phase/0/{UUID}/puppet.log.gz.`

**Common Error Messages**


| Error message | Cause | 
| --- | --- | 
|  Puppet \(err\): Systemd start for emr\-record\-server failed\! journalctl log for emr\-record\-server:  |  EMR Record Server failed to start\. See EMR Record Server logs below\.  | 
|  Puppet \(err\): Systemd start for emr\-record\-server failed\! journalctl log for emrsecretagent:  |  EMR Secret Agent failed to start\. See Check Secret Agent logs below\.  | 
|  /Stage\[main\]/Ranger\_plugins::Ranger\_hive\_plugin/Ranger\_plugins::Prepare\_two\_way\_tls\[configure 2\-way TLS in Hive plugin\]/Exec\[create keystore and truststore for Ranger Hive plugin\]/returns \(notice\): 140408606197664:error:0906D06C:PEM routines:PEM\_read\_bio:no start line:pem\_lib\.c:707:Expecting: ANY PRIVATE KEY  |  The private TLS certificate in Secret Manager for the Apache Ranger plugin certificate is not in the correct format or is not a private certificate\. See [TLS certificates](emr-ranger-admin-tls.md) for certificate formats\.  | 
|  /Stage\[main\]/Ranger\_plugins::Ranger\_s3\_plugin/Ranger\_plugins::Prepare\_two\_way\_tls\[configure 2\-way TLS in Ranger s3 plugin\]/Exec\[create keystore and truststore for Ranger amazon\-emr\-s3 plugin\]/returns \(notice\): An error occurred \(AccessDeniedException\) when calling the GetSecretValue operation: User: arn:aws:sts::XXXXXXXXXXX:assumed\-role/EMR\_EC2\_DefaultRole/i\-XXXXXXXXXXXX is not authorized to perform: secretsmanager:GetSecretValue on resource: arn:aws:secretsmanager:us\-east\-1:XXXXXXXXXX:secret:AdminServer\-XXXXX  |  The EC2 Instance profile role does not have the correct permissions to retrieve the TLS certificates from Secrets Agent\.  | 

**Check SecretAgent logs**

Secret Agent logs are located at `/emr/secretagent/log/` on an EMR node, or in the `s3://<LOG LOCATION>/<CLUSTER ID>/node/<EC2 INSTANCE ID>/daemons/secretagent/` directory in S3\.

**Common Error Messages**


| Error message | Cause | 
| --- | --- | 
|  Exception in thread "main" com\.amazonaws\.services\.securitytoken\.model\.AWSSecurityTokenServiceException: User: arn:aws:sts::XXXXXXXXXXXX:assumed\-role/EMR\_EC2\_DefaultRole/i\-XXXXXXXXXXXXXXX is not authorized to perform: sts:AssumeRole on resource: arn:aws:iam::XXXXXXXXXXXX:role/\*RangerPluginDataAccessRole\* \(Service: AWSSecurityTokenService; Status Code: 403; Error Code: AccessDenied; Request ID: XXXXXXXX\-XXXX\-XXXX\-XXXX\-XXXXXXXXXXXX; Proxy: null\)  |  The above exception means that the EMR EC2 instance profile role does not have permissions to assume the role **RangerPluginDataAccessRole**\. See [IAM roles for native integration with Apache Ranger](emr-ranger-iam.md)\.  | 
|  ERROR qtp54617902\-149: Web App Exception Occurred javax\.ws\.rs\.NotAllowedException: HTTP 405 Method Not Allowed  |  These errors can be safely ignored\.  | 

**Check Record Server Logs \(for SparkSQL\)**

EMR Record Server logs are available at /var/log/emr\-record\-server/ on an EMR node, or they can be found in the s3://<LOG LOCATION>/<CLUSTER ID>/node/<EC2 INSTANCE ID>/daemons/emr\-record\-server/ directory in S3\.

**Common Error Messages**


| Error message | Cause | 
| --- | --- | 
|  InstanceMetadataServiceResourceFetcher:105 \- \[\] Fail to retrieve token com\.amazonaws\.SdkClientException: Failed to connect to service endpoint   |  The EMR SecretAgent failed to come up or is having an issue\. Inspect the SecretAgent logs for errors and the puppet script to determine if there were any provisioning errors\.  | 