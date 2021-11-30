# Enhance kernels with magic commands<a name="emr-studio-magics"></a>

EMR Studio and EMR Notebooks support magic commands, which are enhancements provided by the IPython kernel to help run and analyze data\. IPython is an interactive shell environment built with Python\.

Amazon EMR also supports Sparkmagic, a package that provides Spark\-related kernels \(PySpark, SparkR, and Scala kernels\) with specific magic commands and that uses Livy on the cluster to submit Spark jobs\.

You can use magic commands as long as you have a Python kernel in your EMR notebook\. Similarly, any Spark\-related kernel supports Sparkmagic commands\.

Magic commands, also called *magics*, come in two varieties:
+ **Line magics** – These magic commands are denoted by a single % prefix and operate on a single line of code
+ **Cell magics** – These magic commands are denoted by a double %% prefix and operate on multiple lines of code

A full list of magic commands can be found on the [IPython website](https://ipython.readthedocs.io/en/stable/interactive/magics.html)\. To learn more about Sparkmagic commands, see [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) on the GitHub website\.

## Considerations and limitations<a name="considerations-limitations-magics"></a>

Amazon EMR on EKS clusters do not support Sparkmagic commands for EMR Studio\. This is because Spark kernels used with managed endpoints are built into Kubernetes and are not supported by Sparkmagic and Livy\. You can set the Spark configuration directly into the SparkContext object as a workaround, as the following example demonstrates\.

```
spark.conf.set("spark.driver.maxResultSize", '6g') 
```

AWS doesn't allow the following magic commands because of security concerns:
+ `%alias`
+ `%alias_magic`
+ `%automagic`
+ `%macro`
+ Modifying `proxy_user` using `%configure`
+ Modifying `KERNEL_USERNAME` using `%env` or `%set_env`

## List magic and Sparkmagic commands<a name="accessing-all-magic-commands"></a>

Use the following commands to list the available magic commands:
+ `%lsmagic` lists all currently\-available magic functions
+ `%%help` lists currently\-available Spark\-related magic functions provided by the Sparkmagic package

## Configure Spark with `%%configure`<a name="using-configure-sparkmagic"></a>

One of the most useful Sparkmagic commands is the `%%configure` command, which configures the session creation parameters\. Using `conf` settings, you can configure any Spark configuration mentioned in [Spark’s configuration documentation](https://spark.apache.org/docs/latest/configuration.html)\.

**Example Add external JAR file to EMR Notebooks from Maven repository or Amazon S3**  
You can use the following approach to add an external JAR file dependency to any Spark\-related kernel that’s supported by Sparkmagic\.  

```
%%configure -f
{"conf": {
    "spark.jars.packages": "com.jsuereth:scala-arm_2.11:2.0,ml.combust.bundle:bundle-ml_2.11:0.13.0,com.databricks:dbutils-api_2.11:0.0.3",
    "spark.jars": "s3://DOC-EXAMPLE-BUCKET/my-jar.jar"
    }
}
```

**Example : Configure Hudi**  
You can use the notebook editor to configure your EMR notebook to use Hudi\.  

```
%%configure
{ "conf": {
     "spark.jars": "hdfs://apps/hudi/lib/hudi-spark-bundle.jar,hdfs:///apps/hudi/lib/spark-spark-avro.jar", 
     "spark.serializer": "org.apache.spark.serializer.KryoSerializer",
     "spark.sql.hive.convertMetastoreParquet":"false"
     }
}
```

## Run Spark submit using `%%sh`<a name="using-sh-sparkmagic"></a>

The `%%sh` magic runs shell commands in a subprocess on an instance of your attached cluster\. Typically, you’d use one of the Spark\-related kernels to run Spark applications on your attached cluster\. However, if you want to use a Python kernel to submit a Spark application, you can use the following magic, replacing the bucket name with your bucket name in lowercase\.

```
%%sh
spark-submit --master yarn --deploy-mode cluster s3://DOC-EXAMPLE-BUCKET/test.py
```

In this example, the cluster needs access to the location of `s3://DOC-EXAMPLE-BUCKET/test.py`, or the command will fail\.

You can use any Linux command with the `%%sh` magic\. If you want to run any Spark or YARN commands, use one of the following options to create an `emr-notebook` Hadoop user and grant the user permissions to run the commands:
+ You can explicitly create a new user by running the following commands\.

  ```
  hadoop fs -mkdir /user/emr-notebook
  hadoop fs -chown emr-notebook /user/emr-notebook
  ```
+ You can turn on user impersonation in Livy, which automatically creates the user\. See [Enabling user impersonation to monitor Spark user and job activity](emr-managed-notebooks-spark-monitor.md) for more information\.

## Use EMR Notebooks magics<a name="emr-magics"></a>

Amazon EMR provides the following EMR Notebooks magics that you can use with Python3 and Spark\-based kernels:
+ `%mount_workspace_dir `\- Mounts your Workspace directory to your cluster so that you can import and run code from other files in your Workspace
+ `%umount_workspace_dir` \- Unmounts your Workspace directory from your cluster
+ `%generate_s3_download_url` \- Generates a temporary download link in your notebook output for an Amazon S3 object 

### Prerequisites<a name="emr-magics-prereqs"></a>

Before you install EMR Notebooks magics, complete the following tasks:
+ Make sure that your [Service role for cluster EC2 instances \(EC2 instance profile\)](emr-iam-role-for-ec2.md) has read access for Amazon S3\. The `EMR_EC2_DefaultRole` with the `AmazonElasticMapReduceforEC2Role` managed policy fulfills this requirement\. If you use a custom role or policy, make sure that it has the necessary S3 permissions\.
**Note**  
EMR Notebooks magics run on a cluster as the notebook user and use the EC2 instance profile to interact with Amazon S3\. When you mount a Workspace directory on an EMR cluster, all Workspaces and EMR notebooks with permission to attach to that cluster can access the mounted directory\.  
Directories are mounted as read\-only by default\. While `s3fs-fuse` and `goofys` allow read\-write mounts, we strongly recommend that you do not modify mount parameters to mount directories in read\-write mode\. If you allow write access, any changes made to the directory are written to the S3 bucket\. To avoid accidental deletion or overwriting, you can enable versioning for your S3 bucket\. To learn more, see [Using versioning in S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)\.
+ Run one of the following scripts on your cluster to install the dependencies for EMR Notebooks magics\. To run a script, you can either [Use custom bootstrap actions](emr-plan-bootstrap.md#bootstrapCustom) or follow the instructions in [Run commands and scripts on an Amazon EMR cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-commandrunner.html) when you already have a running cluster\.

  You can choose which dependency to install\. Both [s3fs\-fuse](https://github.com/s3fs-fuse/s3fs-fuse) and [goofys](https://github.com/kahing/goofys) are FUSE \(Filesystem in Userspace\) tools that let you mount an Amazon S3 bucket as a local file system on a cluster\. The `s3fs` tool provides an experience similar to POSIX\. The `goofys` tool is a good choice when you prefer performance over a POSIX\-compliant file system\.

  ```
  #!/bin/sh
  
  # Install the s3fs dependency for EMR Notebooks magics
  sudo amazon-linux-extras install epel -y
  sudo yum install s3fs-fuse -y
  ```

  **OR**

  ```
  #!/bin/sh
  
  # Install the goofys dependency for EMR Notebooks magics
  sudo wget https://github.com/kahing/goofys/releases/latest/download/goofys -P /usr/bin/
  sudo chmod ugo+x /usr/bin/goofys
  ```

### Install EMR Notebooks magics<a name="emr-magics-install"></a>

Complete the following steps to install EMR Notebooks magics\.

1. In your notebook, run the following command to install the [https://pypi.org/project/emr-notebooks-magics/](https://pypi.org/project/emr-notebooks-magics/) package\.

   ```
   %pip install emr-notebooks-magics
   ```

1. Restart your kernel to load the EMR Notebooks magics\.

1. Verify your installation with the following command, which should display output help text for `%mount_workspace_dir`\.

   ```
   %mount_workspace_dir?
   ```

### Mount a Workspace directory with `%mount_workspace_dir`<a name="emr-magics-mount-workspace"></a>

The `%mount_workspace_dir` magic lets you mount your Workspace directory onto your EMR cluster so that you can import and run other files, modules, or packages stored in your directory\.

The following example mounts the entire Workspace directory onto a cluster, and specifies the optional *`<--fuse-type>`* argument to use goofys for mounting the directory\.

```
%mount_workspace_dir . <--fuse-type goofys>
```

To verify that your Workspace directory is mounted, use the following example to display the current working directory with the `ls` command\. The output should display all of the files in your Workspace\.

```
%%sh
ls
```

When you're done making changes in your Workspace, you can unmount the Workspace directory with the following command\.

**Note**  
Your Workspace directory stays mounted to your cluster even when the Workspace is stopped or detached\. You must explicitly unmount your Workspace directory\.

```
%umount_workspace_dir
```

### Download an Amazon S3 object with `%generate_s3_download_url`<a name="emr-magics-generate-s3-download-url"></a>

The `generate_s3_download_url` command creates a presigned URL for an object stored in Amazon S3\. You can use the presigned URL to download the object to your local machine\. For example, you might run `generate_s3_download_url` to download the result of a SQL query that your code writes to Amazon S3\.

The presigned URL is valid for 60 minutes by default\. You can change the expiration time by specifying a number of seconds for the `--expires-in` flag\. For example, `--expires-in 1800` creates a URL that is valid for 30 minutes\.

The following example generates a download link for an object by specifying the full Amazon S3 path: `s3://EXAMPLE-DOC-BUCKET/path/to/my/object`\.

```
%generate_s3_download_url s3://EXAMPLE-DOC-BUCKET/path/to/my/object
```

To learn more about using `generate_s3_download_url`, run the following command to display help text\.

```
%generate_s3_download_url?
```