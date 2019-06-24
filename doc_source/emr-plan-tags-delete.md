# Remove Tags from a Cluster<a name="emr-plan-tags-delete"></a>

If you no longer need a tag, you can remove it from the cluster\. 

**To remove tags from a cluster using the console**

1. In the Amazon EMR console, select the **Cluster List** page and click a cluster from which to remove tags\.

1. On the **Cluster Details** page, in the **Tags** field, click **View All/Edit**\. 

1. In the **View All/Edit** dialog box, click the **X** icon next to the tag to delete and click **Save**\.

1. \(Optional\) Repeat the previous step for each tag key/value pair to remove from the cluster\.

**To remove tags from a cluster using the AWS CLI**

To remove tags from a cluster using the AWS CLI, type the `remove-tags` subcommand with the `--tag-keys` parameter\. When removing a tag, only the key name is required\.
+ To remove a tag from a cluster, type the following command and replace *j\-KT4XXXXXXXX1NM* with your cluster ID\.

  ```
  aws emr remove-tags --resource-id j-KT4XXXXXX1NM --tag-keys "costCenter"
  ```
**Note**  
You cannot currently remove multiple tags using a single command\.

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.