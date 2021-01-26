# Setting Up Amazon EMR<a name="emr-setting-up"></a>

Complete the tasks in this section before you launch an Amazon EMR cluster for the first time:

1. [Sign Up for AWS](#emr-setting-up-signup)

1. [Create an Amazon EC2 Key Pair for SSH](#emr-setting-up-key-pair)

## Sign Up for AWS<a name="emr-setting-up-signup"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create an Amazon EC2 Key Pair for SSH<a name="emr-setting-up-key-pair"></a>

**Note**  
With Amazon EMR release versions 5\.10\.0 or later, you can configure Kerberos to authenticate users and SSH connections to a cluster\. For more information, see [Use Kerberos Authentication](emr-kerberos.md)\.

To authenticate and connect to the nodes in a cluster over a secure channel using the Secure Shell \(SSH\) protocol, create an Amazon Elastic Compute Cloud \(Amazon EC2\) key pair before you launch the cluster\. You can also create a cluster without a key pair\. This is usually done with transient clusters that start, run steps, and then terminate automatically\.


| If\.\.\. | Then\.\.\. | 
| --- | --- | 
|  You already have an Amazon EC2 key pair that you want to use, or you don't need to authenticate to your cluster\.  | Skip this step\. | 
| You need to create a key pair\. | See [Creating Your Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\. | 

## Next Steps<a name="emr-setting-up-next-steps"></a>
+ For guidance on creating a sample cluster, see [Tutorial: Getting Started with Amazon EMR](emr-gs.md)\.
+ For more information on how to configure a custom cluster and control access to it, see [Plan and Configure Clusters](emr-plan.md) and [Security in Amazon EMR](emr-security.md)\.