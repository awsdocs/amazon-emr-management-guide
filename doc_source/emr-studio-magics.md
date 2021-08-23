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

## Accessing all magic and Sparkmagic commands<a name="accessing-all-magic-commands"></a>

Use the following commands to list all available magic commands:
+ `%ismagic` lists all currently\-available magic functions
+ `%%help` lists currently\-available Spark\-related magic functions provided by the Sparkmagic package

## Configuring Spark with `%%configure`<a name="using-configure-sparkmagic"></a>

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

**Example Configure Hudi**  
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

## Spark submit using `%%sh`<a name="using-sh-sparkmagic"></a>

The `%%sh` magic runs shell commands in a subprocess on an instance of your attached cluster\. Typically, you’d use one of the Spark\-related kernels to run Spark applications on your attached cluster\. However, if you want to use a Python kernel to submit a Spark application, you can use the following magic, replacing the bucket name with your bucket name in lowercase\.

```
%%sh
spark-submit --master yarn --deploy-mode cluster s3://DOC-EXAMPLE-BUCKET/test.py
```

In this example, the cluster needs access to the location of `s3://DOC-EXAMPLE-BUCKET/test.py` or the command will fail\.

You can use any Linux command with the `%%sh` magic\. If you want to run any Spark or YARN commands, use one of the following options to create an `emr-notebook` Hadoop user and grant the user permissions to run the commands\.
+ You can explicitly create a new user by running the following commands\.

  ```
  hadoop fs -mkdir /user/emr-notebook
  hadoop fs -chown emr-notebook /user/emr-notebook
  ```
+ You can turn on user impersonation in Livy, which automatically creates the user\. See [Enabling user impersonation to monitor Spark user and job activity](emr-managed-notebooks-spark-monitor.md) for more information\.