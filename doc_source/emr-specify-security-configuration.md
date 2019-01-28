# Specify a Security Configuration for a Cluster<a name="emr-specify-security-configuration"></a>

You can specify encryption settings when you create a cluster by specifying the security configuration\. You can use the AWS Management Console or the AWS CLI\.

## Specifying a Security Configuration Using the Console<a name="emr-specify-security-configuration-console"></a>

When using the AWS console to create an EMR cluster, you choose the security configuration during **Step 4: Security** of the advanced options creation process\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. On the**Step 1: Software and Steps** screen, from the **Release ** list, choose **emr\-4\.8\.0 ** or a more recent release\. Choose the settings you want and choose **Next**\.

1. On the **Step 2: Hardware** screen, choose the settings you want and choose **Next**\. Do the same for **Step 3: General Cluster Settings**\.

1. On the **Step 4: Security** screen, under **Encryption Options**, choose a value for **Security configuration**\.

1. Configure other security options as desired and choose **Create cluster**\.

## Specifying a Security Configuration Using the CLI<a name="emr-specify-security-configuration-cli"></a>

When you use `aws emr create-cluster`, you can optionally apply a security configuration using `--security-configuration MySecConfig`, where `MySecConfig` is the name of the security configuration, as shown in the following example\. The `--release-label` specified must be 4\.8\.0 or later and the `--instance-type` can be any available\.

```
aws emr create-cluster --instance-type m4.large --release-label emr-5.0.0 --security-configuration mySecConfig
```