# Add Tags to a New Cluster<a name="emr-plan-tags-add-new"></a>

You can add tags to a cluster while you are creating it\. 

**To add tags when creating a new cluster using the console**

1. In the Amazon EMR console, select the **Cluster List** page and click **Create cluster**\.

1. On the **Create Cluster** page, in the **Tags** section, click the empty field in the **Key** column and type the name of your key\. 

   When you begin typing the new tag, another tag line automatically appears to provide space for the next new tag\.

1. Optionally, click the empty field in the **Value** column and type the name of your value\. 

1. Repeat the previous steps for each tag key/value pair to add to the cluster\. When the cluster launches, any tags you enter are automatically associated with the cluster\. 

**To add tags when creating a new cluster using the AWS CLI**

The following example demonstrates how to add a tag to a new cluster using the AWS CLI\. To add tags when you create a cluster, type the `create-cluster` subcommand with the `--tags` parameter\. 
+ To add a tag named *costCenter* with key value *marketing* when you create a cluster, type the following command and replace *myKey* with the name of your EC2 key pair\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --applications Name=Hadoop Name=Hive Name=Pig --tags "costCenter=marketing" --use-default-roles --ec2-attributes KeyName=myKey --instance-type m4.large --instance-count 3
  ```

  When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.
**Note**  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.