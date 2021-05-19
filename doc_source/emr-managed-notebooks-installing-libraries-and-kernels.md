# Installing and Using Kernels and Libraries<a name="emr-managed-notebooks-installing-libraries-and-kernels"></a>

Each EMR notebook comes with a set of pre\-installed libraries and kernels\. You can install additional libraries and kernels in an Amazon EMR cluster if the cluster has access to the repository where the kernels and libraries are located\. For example, for clusters in private subnets, you might need to conﬁgure network address translation \(NAT\) and provide a path for the cluster to access the public PyPI repository to install a library\. For more information about configuring external access for different network configurations, see [Scenarios and Examples](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenarios.html) in the *Amazon VPC User Guide*\.

## Installing Kernels and Python Libraries on a Cluster Master Node<a name="emr-managed-notebooks-cluster-kernel"></a>

With Amazon EMR release version 5\.30\.0 and later, excluding 6\.0\.0, you can install additional Python libraries and kernels on the master node of the cluster\. After installation, these kernels and libraries are available to any user running an EMR notebook attached to the cluster\. Python libraries installed this way are available only to processes running on the master node\. The libraries are not installed on core or task nodes and are not available to executors running on those nodes\.

**Note**  
For EMR versions 5\.30\.1, 5\.31\.0, and 6\.1\.0, you must take additional steps in order to install kernels and libraries on the master node of a cluster\.   
To enable the feature, do the following:  
Make sure that the permissions policy attached to the service role for EMR Notebooks allows the following action:  
`elasticmapreduce:ListSteps`  
For more information, see [Service Role for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-service-role.html)\.
Use the AWS CLI to run a step on the cluster that sets up EMR Notebooks as shown in the following example\. You must use the step name `EMRNotebooksSetup`\. Replace *us\-east\-1* with the Region in which your cluster resides\. For more information, see [Adding Steps to a Cluster Using the AWS CLI](https://docs.aws.amazon.com/emr/latest/ManagementGuide/add-step-cli.html)\.  

   ```
   aws emr add-steps  --cluster-id MyClusterID --steps Type=CUSTOM_JAR,Name=EMRNotebooksSetup,ActionOnFailure=CONTINUE,Jar=s3://us-east-1.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://awssupportdatasvcs.com/bootstrap-actions/EMRNotebooksSetup/emr-notebooks-setup.sh"]
   ```

You can install kernels and libraries by running `pip` or `conda` in `/emr/notebook-env/bin` on the master node\. 

You can run `pip` as a terminal command from within a notebook cell\. Using `conda` requires sudo access\. You must connect to the master node using SSH and then run `conda` from the terminal\. For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\.

The following example demonstrates installing the Kotlin kernel using a terminal command while connected to the master node of a cluster:

```
sudo /emr/notebook-env/bin/conda install kotlin-jupyter-kernel -c jetbrains
```

**Note**  
These instructions do not install kernel dependencies\. If your kernel has third\-party dependencies, you may need to take additional setup steps before you can use the kernel with your notebook\.

## Using Notebook\-Scoped Libraries<a name="emr-managed-notebooks-scoped-libraries"></a>

Notebook\-scoped libraries are available using clusters created using Amazon EMR release version 5\.26\.0 or later\. Notebook\-scoped libraries are intended to be used only with the PySpark kernel\. Any user can install additional notebook\-scoped libraries from within a notebook cell\. These libraries are only available to that notebook user during a single notebook session\. If other users need the same libraries, or the same user needs the same libraries in a different session, the library must be re\-installed\.

### Considerations and Limitations<a name="emr-managed-notebooks-custom-libraries-limitations"></a>

Consider the following when using notebook\-scoped libraries:
+ You can uninstall only the libraries that are installed using `install_pypi_package` API\. You cannot uninstall any libraries pre\-installed on the cluster\.
+ If the same libraries with different versions are installed on the cluster and as notebook\-scoped libraries, the notebook\-scoped library version overrides the cluster library version\.

## Working With Notebook\-Scoped Libraries<a name="emr-managed-notebooks-work-with-libraries"></a>

To install libraries, your Amazon EMR cluster must have access to the PyPI repository where the libraries are located\.

The following examples demonstrate simple commands to list, install, and uninstall libraries from within a notebook cell using the PySpark kernel and APIs\. For additional examples, see [Install Python libraries on a running cluster with EMR Notebooks](http://aws.amazon.com/blogs/big-data/install-python-libraries-on-a-running-cluster-with-emr-notebooks/) post on the AWS Big Data Blog\.

**Example – Listing Current Libraries**  
The following command lists the Python packages available for the current Spark notebook session\. This lists libraries installed on the cluster and notebook\-scoped libraries\.  

```
sc.list_packages()
```

**Example – Installing the Celery Library**  
The following command installs the [Celery](https://pypi.org/project/celery/) library as a notebook\-scoped library\.  

```
sc.install_pypi_package("celery")
```
After installing the library, the following command confirms that the library is available on the Spark driver and executors\.  

```
import celery
sc.range(1,10000,1,100).map(lambda x: celery.__version__).collect()
```

**Example – Installing the Arrow Library, Specifying the Version and Repository**  
The following command installs the [Arrow](https://pypi.org/project/arrow/) library as a notebook\-scoped library, with a specification of the library version and repository URL\.  

```
sc.install_pypi_package("arrow==0.14.0", "https://pypi.org/simple")
```

**Example – Uninstalling a Library**  
The following command uninstalls the Arrow library, removing it as a notebook\-scoped library from the current session\.  

```
sc.uninstall_package("arrow")
```