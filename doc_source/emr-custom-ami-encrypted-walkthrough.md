# Tutorial: Creating a custom AMI with an encrypted root device volume using a KMS CMK<a name="emr-custom-ami-encrypted-walkthrough"></a>

The first step in this example is to find the ARN of a KMS CMK or create a new one\. For more information about creating keys, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. The following procedure shows you how to add the default service role, `EMR_DefaultRole`, as a key user to the key policy\. Write down the **ARN** value for the key as you create or edit it\. You use the ARN later, when you create the AMI\.

**To add the service role for Amazon EC2 to the list of encryption key users using the console**

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Choose the alias of the CMK to use\.

1. On the key details page under **Key Users**, choose **Add**\.

1. In the **Attach** dialog box, choose the Amazon EMR service role\. The name of the default role is `EMR_DefaultRole`\.

1. Choose **Attach**\.

**To create an encrypted AMI using the AWS CLI**
+ Use the `aws ec2 copy-image` command from the AWS CLI to create an AMI with an encrypted EBS root device volume and the key that you modified\. Replace the `--kms-key-id` value specified with the full ARN of the key that you created or modified earlier\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws ec2 copy-image --source-image-id MyAmiId \
  --source-region us-west-2 --name MyEncryptedEMRAmi \
  --encrypted --kms-key-id arn:aws:kms:us-west-2:12345678910:key/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  ```

The output of the command provides the ID of the AMI that you created, which you can specify when you create a cluster\. For more information, see [Specifying a custom AMI](emr-specify-custom-ami.md)\. You can also choose to customize this AMI by installing software and performing other configurations\. For more information, see [Creating a custom Amazon Linux AMI from a preconfigured instance](emr-specify-custom-ami.md#emr-custom-ami-preconfigure)\.