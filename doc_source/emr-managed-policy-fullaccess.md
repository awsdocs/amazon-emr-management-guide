# IAM managed policy for full access \(on path to deprecation\)<a name="emr-managed-policy-fullaccess"></a>

The `AmazonElasticMapReduceFullAccess` managed policy grants all the required actions for Amazon EMR and other services\. The `AmazonElasticMapReduceFullAccess` managed policy is on the path to deprecation, but not yet deprecated\. When the policy is eventually deprecated, you will not abe able to attach it to a role\. However, a role that already has an attached deprecated policy can still be attached to a cluster\. `AmazonElasticMapReduceFullAccess` has been replaced with [`AmazonEMRFullAccessPolicy_v2`](emr-managed-policy-fullaccess-v2.md) as the Amazon EMR default managed policy\.

The Amazon EMR full permissions default managed policy \(`AmazonEMRFullAccessPolicy_v2`\) and Amazon EMR service policy \(`AmazonEMRServicePolicy_v2`\) are available to replace the soon\-to\-be\-deprecated policy\. The v2 policies incorporate new iam:PassRole security configurations, including the following:
+ `iam:PassRole` permissions only for specific default Amazon EMR roles\.
+ `iam:PassedToService` conditions that allow you to use the policy with only specified AWS services, such as `elasticmapreduce.amazonaws.com` and `ec2.amazonaws.com`\.

You can view the JSON version of the [AmazonEMRFullAccessPolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRFullAccessPolicy_v2) and [AmazonEMRServicePolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRServicePolicy_v2) policies in the IAM console\.

We recommend that you create new clusters using v2 managed policies\.

You can view the contents of this policy using the AWS Management Console link [https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonElasticMapReduceFullAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonElasticMapReduceFullAccess)\.

**Note**  
The `ec2:TerminateInstances` action in the policy enables the IAM user or role that assumes this policy to terminate any of the Amazon EC2 instances associated with the IAM account, even those that are not part of an Amazon EMR cluster\.