# Work with Steps Using the CLI and Console<a name="emr-work-with-steps"></a>

You can add steps to a cluster using the AWS Management Console, the AWS CLI, or the Amazon EMR API\. The maximum number of PENDING and ACTIVE steps allowed in a cluster is 256, which includes system steps such as install Pig, install Hive, install HBase, and configure debugging\. You can submit an unlimited number of steps over the lifetime of a long\-running cluster, but only 256 steps can be ACTIVE or PENDING at any given time\. With EMR version 4\.8\.0 and later, except version 5\.0\.0, you can cancel steps that are PENDING using the AWS Management Console, the AWS CLI, or the Amazon EMR API\.

## Adding Steps to a Cluster<a name="emr-add-steps"></a>

You can add steps to a cluster using the AWS CLI, the Amazon EMR SDK, or the AWS Management Console\. Using the AWS Management Console, you can add steps to a cluster when the cluster is created\. You can also add steps to a long\-running cluster—that is, a cluster with the auto\-terminate option disabled\.

### Add Steps Using the Console<a name="add-steps-console"></a>

Whether you add steps during cluster creation or to a cluster, the procedure is similar to the following\.

**To add a step to a running cluster using the AWS Management Console**

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, click the link for your cluster\. 

1. On the **Cluster Details** page, expand the **Steps** section, and then click **Add step**\. 

1. Type appropriate values in the fields in the **Add Step** dialog, and then click **Add**\. Depending on the step type, the options are different\. 

### Add Steps Using the AWS CLI<a name="add-step-cli"></a>

The following procedures demonstrate adding steps to a newly\-created cluster and to a running cluster using the AWS CLI\. In both examples, the `--steps` subcommand is used to add steps to the cluster\. 

**To add a step during cluster creation**
+ Type the following command to create a cluster and add a Pig step\. Replace *myKey* with the name of your EC2 key pair and replace *mybucket* with the name of your Amazon S3 bucket\.
  + Linux, UNIX, and Mac OS X users:

    ```
    1. aws emr create-cluster --name "Test cluster" --ami-version 2.4 --applications Name=Hive Name=Pig \
    2. --use-default-roles --ec2-attributes KeyName=myKey \
    3. --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m4.large InstanceGroupType=CORE,InstanceCount=2,InstanceType=m4.large \
    4. --steps Type=PIG,Name="Pig Program",ActionOnFailure=CONTINUE,Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,$INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/]
    ```
  + Windows users:

    ```
    1. aws emr create-cluster --name "Test cluster" --ami-version 2.4 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m4.large InstanceGroupType=CORE,InstanceCount=2,InstanceType=m4.large --steps Type=PIG,Name="Pig Program",ActionOnFailure=CONTINUE,Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,$INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/]
    ```
**Note**  
The list of arguments changes depending on the type of step\.

  The output is a cluster identifier similar to the following: 

  ```
  1. {
  2.     "ClusterId": "j-2AXXXXXXGAPLF"
  3. }
  ```

**To add a step to a running cluster**
+ Type the following command to add a step to a running cluster\. Replace *j\-2AXXXXXXGAPLF* with your cluster ID and replace *mybucket* with your Amazon S3 bucket name\. 

  ```
  aws emr add-steps --cluster-id j-2AXXXXXXGAPLF --steps Type=PIG,Name="Pig Program",Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,$INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/] 
  ```

   The output is a step identifier similar to the following:

  ```
  1. {
  2.     "StepIds": [
  3. 	"s-Y9XXXXXXAPMD"
  4.     ]
  5. }
  ```

For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Viewing Steps<a name="emr-view-steps"></a>

The total number of step records you can view \(regardless of status\) is 1,000\. This total includes both user\-submitted and system steps\. As the status of user\-submitted steps changes to COMPLETED or FAILED, additional user\-submitted steps can be added to the cluster until the 1,000 step limit is reached\. After 1,000 steps have been added to a cluster, the submission of additional steps causes the removal of older, user\-submitted step records\. These records are not removed from the log files\. They are removed from the console display, and they do not appear when you use the CLI or API to retrieve cluster information\. System step records are never removed\.

The step information you can view depends on the mechanism used to retrieve cluster information\. The following tables indicate the step information returned by each of the available options\. 


| Option | DescribeJobFlow or \-\-describe \-\-jobflow | ListSteps or list\-steps | 
| --- | --- | --- | 
| SDK | 256 steps | 1,000 steps | 
| Amazon EMR CLI | 256 steps | NA | 
| AWS CLI | NA | 1,000 steps | 
| API | 256 steps | 1,000 steps | 

## Cancel Pending Steps<a name="emr-cancel-steps"></a>

You can cancel steps using the the AWS Management Console, the AWS CLI, or the Amazon EMR API\. Only steps that are `PENDING` can be canceled\.

**To cancel steps using the AWS Management Console**

1. In the [Amazon EMR console](https://console.aws.amazon.com/elasticmapreduce), on the **Cluster List** page, choose the link for the cluster\. 

1. On the **Cluster Details** page, expand the **Steps** section\.

1. For each step you want to cancel, select the step from the list of **Steps**, select **Cancel step**, and then confirm you want to cancel the step\.

**To cancel steps using the AWS CLI**
+ Use the `aws emr cancel-steps` command, specifying the cluster and steps to cancel\. The following example demonstrates an AWS CLI command to cancel two steps\.

  ```
  aws emr cancel-steps --cluster-id j-2QUAJ7T3OTEI8 --step-ids s-3M8DKCZYYN1QE,s-3M8DKCZYYN1QE
  ```