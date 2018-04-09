# Launch a Cluster with MapR Using the AWS CLI<a name="emr-mapr-launch-cli"></a>

**To launch an Amazon EMR cluster with MapR using the AWS CLI**
**Note**  
You cannot yet launch MapR clusters when specifying the `--release-label` parameter\.

To launch a cluster with MapR using the AWS CLI, type the `create-cluster` command with the `--applications` parameter\.
+ To launch a cluster with MapR, m7 edition, type the following command and replace *myKey* with the name of your EC2 key pair\.
  + Linux, UNIX, and Mac OS X users:

    ```
    1. aws emr create-cluster --name "MapR cluster" --applications Name=Hive Name=Pig \
    2. Name=MapR,Args=--edition,m7,--version,4.0.2 --ami-version 3.3.2 \
    3. --use-default-roles --ec2-attributes KeyName=myKey \
    4. --instance-type m3.xlarge --instance-count 3
    ```
  + Windows users:

    ```
    1. aws emr create-cluster --name "MapR cluster" --applications Name=Hive Name=Pig Name=MapR,Args=--edition,m7,--version,3.1.1 --ami-version 2.4 --use-default-roles --ec2-attributes KeyName=myKey --instance-type m3.xlarge --instance-count 3
    ```
**Note**  
The version of MapR available in Amazon EMR that supports Hadoop 2\.x is 4\.0\.2, and it is only available with Amazon EMR AMI 3\.3\.2

  When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.
**Note**  
If you have not previously created the default EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

  For more information on using Amazon EMR commands in the AWS CLI, see [http://docs.aws.amazon.com/cli/latest/reference/emr](http://docs.aws.amazon.com/cli/latest/reference/emr)\.