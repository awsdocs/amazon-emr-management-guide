# Retry Logic<a name="emrfs-retry-logic"></a>

EMRFS tries to verify list consistency for objects tracked in its metadata for a specific number of retries\. The default is 5\. In the case where the number of retries is exceeded the originating job returns a failure unless `fs.s3.consistent.throwExceptionOnInconsistency` is set to `false`, where it will only log the objects tracked as inconsistent\. EMRFS uses an exponential backoff retry policy by default but you can also set it to a fixed policy\. Users may also want to retry for a certain period of time before proceeding with the rest of their job without throwing an exception\. They can achieve this by setting `fs.s3.consistent.throwExceptionOnInconsistency` to `false`, `fs.s3.consistent.retryPolicyType` to `fixed`, and `fs.s3.consistent.retryPeriodSeconds` for the desired value\. The following example creates a cluster with consistency enabled, which logs inconsistencies and sets a fixed retry interval of 10 seconds:

**Example Setting retry period to a fixed amount**  

```
aws emr create-cluster --release-label emr-5.29.0 \
--instance-type m5.xlarge --instance-count 1 \
--emrfs Consistent=true,Args=[fs.s3.consistent.throwExceptionOnInconsistency=false, fs.s3.consistent.retryPolicyType=fixed,fs.s3.consistent.retryPeriodSeconds=10] --ec2-attributes KeyName=myKey
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

For more information, see [Consistent View](emr-plan-consistent-view.md)\.