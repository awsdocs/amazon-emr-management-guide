# Managing the default AWS Security Token Service endpoint<a name="emr-emrfs-sts-endpoint"></a>

EMRFS uses the AWS Security Token Service \(STS\) to retrieve temporary security credentials in order to access your AWS resources\. Earlier Amazon EMR release versions send all AWS STS requests to a single global endpoint at `https://sts.amazonaws.com`\. Amazon EMR release versions 5\.31\.0 and 6\.1\.0 and later make requests to Regional AWS STS endpoints instead\. This reduces latency and improves session token validity\. For more information about AWS STS endpoints, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *AWS Identity and Access Management User Guide*\.

When you use Amazon EMR release versions 5\.31\.0 and 6\.1\.0 and later, you can override the default AWS STS endpoint\. To do so, you must change the `fs.s3.sts.endpoint` property in your `emrfs-site` configuration\.

The following AWS CLI example sets the default AWS STS endpoint used by EMRFS to the global endpoint\.

```
aws emr create-cluster --release-label <emr-5.33.0> --instance-type m5.xlarge \
--emrfs Args=[fs.s3.sts.endpoint=https://sts.amazonaws.com]
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

Alternatively, you can create a JSON configuration file using the following example, and specify it using the `--configurations` argument of `emr create-cluster`\. For more information about using `--configurations,` see the [*AWS CLI Command Reference*\.](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/emr/create-cluster.html)

```
[
  {
    "classification": "emrfs-site",
    "properties": {
      "fs.s3.sts.endpoint": "https://sts.amazonaws.com"
    }
  }
]
```