# Adding Tags to an Existing Cluster<a name="emr-plan-tags-add"></a>

You can also add tags to an existing cluster\.

**To add tags to an existing cluster using the console**

1. In the Amazon EMR console, select the **Cluster List** page and click a cluster to which to add tags\.

1. On the **Cluster Details** page, in the **Tags** field, click **View All/Edit**\. 

1. On the **View All/Edit** page, click **Add**\. 

1. Click the empty field in the **Key** column and type the name of your key\. 

1. Optionally, click the empty field in the **Value** column and type the name of your value\. 

1. With each new tag you begin, another empty tag line appears under the tag you are currently editing\. Repeat the previous steps on the new tag line for each tag to add\. 

**To add tags to a running cluster using the AWS CLI**

The following example demonstrates how to add tags to a running cluster using the AWS CLI\. Type the `add-tags` subcommand with the `--tag` parameter to assign tags to a resource identifier \(cluster ID\)\. The resource ID is the cluster identifier available via the console or the `list-clusters` command\.
**Note**  
The `add-tags` subcommand currently accepts only one resource ID\.
+ To add two tags to a running cluster \(one with a key named *production* with no value and the other with a key named *costCenter* with a value of *marketing*\) type the following command and replace *j\-KT4XXXXXXXX1NM* with your cluster ID\. 

  ```
  aws emr add-tags --resource-id j-KT4XXXXXXXX1NM --tag "costCenter=marketing" --tag "other=accounting"
  ```
**Note**  
When tags are added using the AWS CLI, there is no output from the command\.

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.