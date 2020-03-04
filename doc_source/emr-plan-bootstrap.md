# Create Bootstrap Actions to Install Additional Software<a name="emr-plan-bootstrap"></a>

You can use a *bootstrap action* to install additional software or customize the configuration of cluster instances\. Bootstrap actions are scripts that run on cluster after Amazon EMR launches the instance using the Amazon Linux Amazon Machine Image \(AMI\)\. Bootstrap actions run before Amazon EMR installs the applications that you specify when you create the cluster and before cluster nodes begin processing data\. If you add nodes to a running cluster, bootstrap actions also run on those nodes in the same way\. You can create custom bootstrap actions and specify them when you create your cluster\. 

Most predefined bootstrap actions for Amazon EMR AMI versions 2\.x and 3\.x are not supported in Amazon EMR releases 4\.x\. For example, `configure-Hadoop` and `configure-daemons` are not supported in Amazon EMR release 4\.x\. Instead, Amazon EMR release 4\.x natively provides this functionality\. For more information about how to migrate bootstrap actions from Amazon EMR AMI versions 2\.x and 3\.x to Amazon EMR release 4\.x, go to [Differences in Amazon EMR 4\.x Release Versions](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-differences.html) in the Amazon EMR Release Guide\.

**Topics**
+ [Bootstrap Action Basics](#bootstrapUses)
+ [Run If Bootstrap Action](#emr-bootstrap-runif)
+ [Shutdown Actions](#bootstrap_Shutown)
+ [Use Custom Bootstrap Actions](#bootstrapCustom)

## Bootstrap Action Basics<a name="bootstrapUses"></a>

Bootstrap actions execute as the Hadoop user by default\. You can execute a bootstrap action with root privileges by using `sudo`\. 

All Amazon EMR management interfaces support bootstrap actions\. You can specify up to 16 bootstrap actions per cluster by providing multiple `bootstrap-actions` parameters from the console, AWS CLI, or API\. 

From the Amazon EMR console, you can optionally specify a bootstrap action while creating a cluster\.

When you use the CLI, you can pass references to bootstrap action scripts to Amazon EMR by adding the `--bootstrap-actions` parameter when you create the cluster using the `create-cluster` command\. The syntax for a `--bootstrap-actions` parameter is as follows: 

**AWS CLI**

```bash
--bootstrap-actions Path=s3://mybucket/filename",Args=[arg1,arg2]
```

If the bootstrap action returns a nonzero error code, Amazon EMR treats it as a failure and terminates the instance\. If too many instances fail their bootstrap actions, then Amazon EMR terminates the cluster\. If just a few instances fail, Amazon EMR attempts to reallocate the failed instances and continue\. Use the cluster `lastStateChangeReason` error code to identify failures caused by a bootstrap action\.

## Run If Bootstrap Action<a name="emr-bootstrap-runif"></a>

Amazon EMR provides this predefined bootstrap action to run a command conditionally when an instance\-specific value is found in the `instance.json` or `job-flow.json` file\. The command can refer to a file in Amazon S3 that Amazon EMR can download and execute\. 

The location of the script is `s3://elasticmapreduce/bootstrap-actions/run-if`\. 

The following example echoes the string "running on master node" if the node is a master\.

**To run a command conditionally using the AWS CLI**

When using the AWS CLI to include a bootstrap action, specify the `Path` and `Args` as a comma\-separated list\.
+ To launch a cluster with a bootstrap action that conditionally runs a command when an instance\-specific value is found in the `instance.json` or `job-flow.json` file, type the following command and replace *myKey* with the name of your EC2 key pair\.

  ```
  1. aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --use-default-roles --ec2-attributes KeyName=myKey --applications Name=Hive --instance-count 1 --instance-type m5.xlarge --bootstrap-actions Path=s3://elasticmapreduce/bootstrap-actions/run-if,Args=["instance.isMaster=true","echo running on master node"]
  ```

  When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.
**Note**  
If you have not previously created the default Amazon EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Shutdown Actions<a name="bootstrap_Shutown"></a>

A bootstrap action script can create one or more shutdown actions by writing scripts to the `/mnt/var/lib/instance-controller/public/shutdown-actions/` directory\. When a cluster is terminated, all the scripts in this directory are executed in parallel\. Each script must run and complete within 60 seconds\. 

Shutdown action scripts are not guaranteed to run if the node terminates with an error\. 

**Note**  
When using Amazon EMR versions 4\.0 and later, you must manually create the `/mnt/var/lib/instance-controller/public/shutdown-actions/` directory on the master node\. It doesn't exist by default; however, after being created, scripts in this directory nevertheless run before shutdown\. For more information about connecting to the Master node to create directories, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\.

## Use Custom Bootstrap Actions<a name="bootstrapCustom"></a>

You can create a custom script to perform a customized bootstrap action\. Any of the Amazon EMR interfaces can reference a custom bootstrap action\.

**Topics**
+ [Add Custom Bootstrap Actions Using the AWS CLI or the Amazon EMR CLI](#CustombootstrapCLI)
+ [Add Custom Bootstrap Actions Using the Console](#CustombootstrapConsole)
+ [Use a Custom Bootstrap Action to Copy an Object from Amazon S3 to Each Node](#CustomBootstrapCopyS3Object)

### Add Custom Bootstrap Actions Using the AWS CLI or the Amazon EMR CLI<a name="CustombootstrapCLI"></a>

The following example uses a bootstrap action script to download and extract a compressed TAR archive from Amazon S3\. The sample script is stored at [ http://elasticmapreduce\.s3\.amazonaws\.com/bootstrap\-actions/download\.sh](http://elasticmapreduce.s3.amazonaws.com/bootstrap-actions/download.sh)\. 

 The sample script looks like the following: 

```bash
1. #!/bin/bash
2. set -e
3. wget -S -T 10 -t 5 http://elasticmapreduce.s3.amazonaws.com/bootstrap-actions/file.tar.gz
4. mkdir -p /home/hadoop/contents
5. tar -xzf file.tar.gz -C /home/hadoop/contents
```

**To create a cluster with a custom bootstrap action using the AWS CLI**

When using the AWS CLI to include a bootstrap action, specify the `Path` and `Args` as a comma\-separated list\. The following example does not use an arguments list\.
+ To launch a cluster with a custom bootstrap action, type the following command, replace *myKey* with the name of your EC2 key pair\. 
  + Linux, UNIX, and Mac OS X users:

    ```
    1. aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 \
    2. --use-default-roles --ec2-attributes KeyName=myKey \
    3. --applications Name=Hive Name=Pig \
    4. --instance-count 3 --instance-type m5.xlarge \
    5. --bootstrap-actions Path="s3://elasticmapreduce/bootstrap-actions/download.sh"
    ```
  + Windows users:

    ```
    1. aws emr create-cluster --name "Test cluster" --release-label emr-4.2.0 --use-default-roles --ec2-attributes KeyName=myKey --applications Name=Hive Name=Pig --instance-count 3 --instance-type m5.xlarge --bootstrap-actions Path="s3://elasticmapreduce/bootstrap-actions/download.sh"
    ```

  When you specify the instance count without using the `--instance-groups` parameter, a single Master node is launched, and the remaining instances are launched as core nodes\. All nodes will use the instance type specified in the command\.
**Note**  
If you have not previously created the default Amazon EMR service role and EC2 instance profile, type aws `emr create-default-roles` to create them before typing the `create-cluster` subcommand\.

  For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

### Add Custom Bootstrap Actions Using the Console<a name="CustombootstrapConsole"></a>

The following procedure describes how to use your own custom bootstrap action\.

**To create a cluster with a custom bootstrap action using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Click **Go to advanced options**\.

1. In Create Cluster \- Advanced Options, Steps 1 and 2 choose the options as desired and proceed to **Step 3: General Cluster Settings**\.

1. Under **Bootstrap Actions** select **Configure and add** to specify the Name, JAR location, and arguments for your bootstrap action\. Choose **Add**\.

1. Optionally add more bootstrap actions as desired\.

1. Proceed to create the cluster\. Your bootstrap action\(s\) will be performed after the cluster has been provisioned and initialized\.

While the cluster's master node is running, you can connect to the master node and see the log files that the bootstrap action script generated in the `/mnt/var/log/bootstrap-actions/1` directory\.

**Related Topics**
+ [View Log Files](emr-manage-view-web-log-files.md)

### Use a Custom Bootstrap Action to Copy an Object from Amazon S3 to Each Node<a name="CustomBootstrapCopyS3Object"></a>

You can use a bootstrap action to copy objects from Amazon S3 to each node in a cluster before your applications are installed\. The AWS CLI is installed on each node of a cluster, so your bootstrap action can call AWS CLI commands\.

The following example demonstrates a simple bootstrap action script that copies a file, `myfile.jar`, from Amazon S3 to a local folder, `/mnt1/myfolder`, on each cluster node\. The script is saved to Amazon S3 with the file name `copymyfile.sh` with the following contents\.

```bash
#!/bin/bash
aws s3 cp s3://mybucket/myfilefolder/myfile.jar /mnt1/myfolder
```

When you launch the cluster, you specify the script\. The following AWS CLI example demonstrates this:

```bash
aws emr create-cluster --name "Test cluster" --release-label emr-5.29.0 \
--use-default-roles --ec2-attributes KeyName=myKey \
--applications Name=Hive Name=Pig \
--instance-count 3 --instance-type m5.xlarge \
--bootstrap-actions Path="s3://mybucket/myscriptfolder/copymyfile.sh"
```
