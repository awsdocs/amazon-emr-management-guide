# Common Errors in Amazon EMR<a name="emr-troubleshoot-errors"></a>

There are many reasons why a cluster might fail or be slow in processing data\. The following sections list the most common issues and suggestions for fixing them\.

**Topics**
+ [Input and Output Errors](emr-troubleshoot-errors-io.md)
+ [Permissions Errors](emr-troubleshoot-error-permissions.md)
+ [Resource Errors](emr-troubleshoot-error-resource.md)
+ [Streaming Cluster Errors](emr-troubleshoot-error-streaming.md)
+ [Custom JAR Cluster Errors](emr-troubleshoot-error-custom-jar.md)
+ [Hive Cluster Errors](emr-troubleshoot-error-hive.md)
+ [VPC Errors](emr-troubleshoot-error-vpc.md)
+ [AWS GovCloud \(US\) Errors](emr-troubleshoot-error-govcloud.md)
+ [Other Issues](#w3ab1c24c33c46)

## Other Issues<a name="w3ab1c24c33c46"></a>

### Do you not see the cluster you expect in the Cluster List page or in results returned from ListClusters API?<a name="w3ab1c24c33c46b2"></a>

Check the following:
+ The cluster age is less than two months\. Amazon EMR preserves metadata information about completed clusters for your reference, at no charge, for two months\. The console does not provide a way to delete completed clusters from the console; these are automatically removed for you after two months\.
+ You have permissions to view the cluster\. If the `VisibleToAllUsers` property is set to false, other users in the same IAM account will not be able to view a cluster\.
+ You are viewing the correct region\.