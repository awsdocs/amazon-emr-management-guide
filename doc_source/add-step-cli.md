# Adding Steps to a Cluster Using the AWS CLI<a name="add-step-cli"></a>

The following procedures demonstrate adding steps to a newly created cluster and to a running cluster using the AWS CLI\. In both examples, the `--steps` subcommand is used to add steps to the cluster\. 

**To add steps during cluster creation**
+ Type the following command to create a cluster and add an Apache Pig step\. Replace *myKey* with the name of your Amazon EC2 key pair and replace *mybucket* with the name of your Amazon S3 bucket\.
  + Linux, UNIX, and macOS

    ```
    1. aws emr create-cluster --name "Test cluster" --ami-version 2.4 --applications Name=Hive Name=Pig \
    2. --use-default-roles --ec2-attributes KeyName=myKey \
    3. --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=2,InstanceType=m5.xlarge \
    4. --steps Type=PIG,Name="Pig Program",ActionOnFailure=CONTINUE,Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,$INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/]
    ```
  + Windows

    ```
    1. aws emr create-cluster --name "Test cluster" --ami-version 2.4 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=2,InstanceType=m5.xlarge --steps Type=PIG,Name="Pig Program",ActionOnFailure=CONTINUE,Args=[-f,s3://mybucket/scripts/pigscript.pig,-p,INPUT=s3://mybucket/inputdata/,-p,OUTPUT=s3://mybucket/outputdata/,$INPUT=s3://mybucket/inputdata/,$OUTPUT=s3://mybucket/outputdata/]
    ```
**Note**  
The list of arguments changes depending on the type of step\.

  By default, the step concurrency level is `1`\. You can set the step concurrency level by using the `StepConcurrencyLevel` parameter when you create a cluster\. 

  The output is a cluster identifier similar to the following\. 

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

   The output is a step identifier similar to the following\.

  ```
  1. {
  2.     "StepIds": [
  3. 	"s-Y9XXXXXXAPMD"
  4.     ]
  5. }
  ```

**To modify the StepConcurrencyLevel in a running cluster**

1. In a running cluster, you can modify the StepConcurrencyLevel by using the `ModifyCluster` API\. For example, type the following command to increase the StepConcurrenyLevel to `10`\. Replace *j\-2AXXXXXXGAPLF* with your cluster ID\.

   ```
   aws emr modify-cluster --cluster-id j-2AXXXXXXGAPLF --step-concurrency-level 10
   ```

1. The output is similar to the following\.

   ```
   {
   "StepConcurrencyLevel": 10
   }
   ```

For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.