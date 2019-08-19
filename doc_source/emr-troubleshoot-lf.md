# Troubleshoot a Lake Formation Cluster \(Beta\)<a name="emr-troubleshoot-lf"></a>

 This section walks you through the process of troubleshooting common issues when using Amazon EMR with AWS Lake Formation\. 

## Session Expiration<a name="emr-troubleshoot-lf-expiration"></a>

The session timeout for EMR Notebooks and Zeppelin is controlled by the IAM Role for Lake Formation’s `Maximum CLI/API session duration` setting\. The default value for this setting is one hour\. When a session timeout occurs, you will see the following message in the output of your notebook entries when trying to run Spark SQL commands\.

```
Error 401    HTTP ERROR: 401 Problem accessing /sessions/2/statements. 
Reason:  JWT token included in request failed validation. 
Powered by Jetty:// 9.3.24.v20180605   org.springframework.web.client.HttpClientErrorException: 401 JWT token included in request failed validation…
```

To validate your session, refresh the page\. You will be prompted to re\-authenticate using your IdP and be redirected back to the Notebook\. You can continue to run queries after re\-authentication\.

## No Permissions for User on Requested Table<a name="emr-troubleshoot-lf-no-permissisons"></a>

When attempting to access a table that you do not have access to, you will see the following exception in the output of your notebook entries when trying to run Spark SQL commands\.

```
org.apache.spark.sql.AnalysisException: org.apache.hadoop.hive.ql.metadata.HiveException: Unable to fetch table table. 
Resource does not exist or requester is not authorized to access requested permissions. 
(Service: AWSGlue; Status Code: 400; Error Code: AccessDeniedException; Request ID: …
```

To access the table, you must grant access to the user by updating the permissions associated with this table in Lake Formation\.

## Inserting Into, Creating and Altering Tables: Unsupported in Beta<a name="emr-troubleshoot-lf-unsupported"></a>

Inserting into, creating, or altering tables in databases protected by Lake Formation policies is not supported\. When performing these operations, you will see the following exception in the output of your notebook entries when trying to run Spark SQL commands:

```
java.io.IOException: com.amazon.ws.emr.hadoop.fs.shaded.com.amazonaws.services.s3.model.AmazonS3Exception: 
            Access Denied (Service: Amazon S3; Status Code: 403; Error Code: AccessDenied; Request ID: …
```

For more information, see [Limitations of Amazon EMR Integration with AWS Lake Formation](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-lf-scope.html#emr-lf-limitations)\.