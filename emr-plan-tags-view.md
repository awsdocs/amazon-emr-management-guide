# View Tags on a Cluster<a name="emr-plan-tags-view"></a>

If you would like to see all the tags associated with a cluster, you can view them in the console or at the CLI\.

**To view the tags on a cluster using the console**

1. In the Amazon EMR console, select the **Cluster List** page and click a cluster to view tags\.

1. On the **Cluster Details** page, in the **Tags** field, some tags are displayed here\. Click **View All/Edit** to display all available tags on the cluster\.

**To view the tags on a cluster using the AWS CLI**

To view the tags on a cluster using the AWS CLI, type the `describe-cluster` subcommand with the `--query` parameter\. 
+ To view a cluster's tags, type the following command and replace *j\-KT4XXXXXXXX1NM* with your cluster ID\.

  ```
  1. aws emr describe-cluster --cluster-id j-KT4XXXXXX1NM --query Cluster.Tags
  ```

  The output displays all the tag information about the cluster similar to the following:

  ```
  Value: accounting     Value: marketing                
  Key: other            Key: costCenter
  ```

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.