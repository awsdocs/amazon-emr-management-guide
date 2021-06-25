# Common errors in Amazon EMR<a name="emr-troubleshoot-errors"></a>

There are many reasons why a cluster might fail or be slow in processing data\. The following sections list the most common issues and suggestions for fixing them\.

**Topics**
+ [Input and output errors](emr-troubleshoot-errors-io.md)
+ [Permissions errors](emr-troubleshoot-error-permissions.md)
+ [Resource errors](emr-troubleshoot-error-resource.md)
+ [Streaming cluster errors](emr-troubleshoot-error-streaming.md)
+ [Custom JAR cluster errors](emr-troubleshoot-error-custom-jar.md)
+ [Hive cluster errors](emr-troubleshoot-error-hive.md)
+ [VPC errors](emr-troubleshoot-error-vpc.md)
+ [AWS GovCloud \(US\-West\) errors](emr-troubleshoot-error-govcloud.md)
+ [Other issues](#w299aac31c31c43)

## Other issues<a name="w299aac31c31c43"></a>

### Do you not see the cluster you expect in the Cluster List page or in results returned from ListClusters API?<a name="w299aac31c31c43b2"></a>

Check the following:
+ The cluster age is less than two months\. Amazon EMR preserves metadata information about completed clusters for your reference, at no charge, for two months\. The console does not provide a way to delete completed clusters from the console; these are automatically removed for you after two months\.
+ You have permissions to view the cluster\.
+ You are viewing the correct region\.