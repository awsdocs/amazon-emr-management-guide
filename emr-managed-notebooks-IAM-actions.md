# IAM Policy Actions for EMR Notebooks<a name="emr-managed-notebooks-IAM-actions"></a>

The actions listed in the following table are related to EMR Notebooks\. For a user to perform an action, an IAM policy must be associated with the principal \(user\) that allows the action\. For tagging purposes, some actions are performed on clusters, notebooks, or both\. This is important for resource tagging\. For more information, see [Using Tags to Control User Permissions](emr-managed-notebooks-tags.md)\.


| Action | Description | Condition Key \- Resource | 
| --- | --- | --- | 
|  `elasticmapreduce:CreateEditor`  |  Create an EMR notebook\.  |  `RequestTag` \- Notebooks `ResourceTag` \- Clusters  | 
|  `elasticmapreduce:StartEditor`  |  Start an EMR notebook\.  |  `ResourceTag` \- Notebooks, Clusters  | 
|  `elasticmapreduce:StopEditor`  |  Stop an EMR notebook\.  |  `ResourceTag` \- Notebooks  | 
|  `elasticmapreduce:OpenEditorInConsole`  |  Open the notebook editor from within the console\.  |  `ResourceTag` \- Notebooks, Clusters  | 
|  `elasticmapreduce:DescribeEditor`  |  List an EMR notebook and view notebook properties\.  |  `ResourceTag` \- Notebooks  | 
|  `elasticmapreduce:DeleteEditor`  |  Delete an EMR notebook using the console\.  This does not affect a user's ability to delete notebook files from Amazon S3\. Use IAM policies for Amazon S3\. For more information, see [Using Bucket Policies and User Policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-iam-policies.html) in the *Amazon Simple Storage Service Developer Guide*\.   |  `ResourceTag` \- Notebooks  | 

**Example –Policy Statement to Allow Full Access to EMR Notebooks**  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "iam:PassRole",
                "iam:ListRoles",
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:ListEditors",
                "elasticmapreduce:StartEditor",
                "elasticmapreduce:StopEditor",
                "elasticmapreduce:DeleteEditor",
                "elasticmapreduce:OpenEditorInConsole",
                "elasticmapreduce:CreateEditor",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:DescribeCluster",
                "s3:ListBucket",
                "s3:ListAllMyBuckets",
                "s3:GetBucketLocation"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```