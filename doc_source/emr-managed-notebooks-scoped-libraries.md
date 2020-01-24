# Using Notebook\-Scoped Libraries<a name="emr-managed-notebooks-scoped-libraries"></a>

If the default libraries available with EMR Notebooks on your cluster are not sufficient for your application, with Amazon EMR 5\.26\.0 and later, you can install *notebook\-scoped* Python libraries from within the notebook editor from public or private Python Package Index \(PyPI\) repositories\. Notebook\-scoped libraries are installed in a virtual Python environment scoped to the current notebook session\. They are available only during the notebook session\. After the session ends, the libraries are deleted\. Subsequent sessions are not able to use the libraries\. To have libraries available across EMR notebook sessions, you can install them on cluster instances using a bootstrap action or a custom Amazon Linux AMI for EMR\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md) and [Using a Custom AMI](emr-custom-ami.md)\.

Notebook\-scoped libraries provide the following benefits:
+ You can use libraries in an EMR notebook without having to re\-create the cluster or re\-attach the notebook to a cluster\.
+ You can isolate library dependencies of an EMR notebook to the individual notebook session\. The libraries installed from within the notebook cannot interfere with other libraries on the cluster or libraries installed within other notebook sessions\.

## Considerations and Limitations<a name="emr-managed-notebooks-custom-libraries-limitations"></a>

Consider the following when using notebook\-scoped libraries:
+ You can uninstall only the libraries that are installed using `install_pypi_package` API\. You cannot uninstall any libraries installed on the cluster\.
+ If the same libraries with different versions are installed on the cluster and as notebook\-scoped libraries, the notebook\-scoped library version overrides the cluster library version\.

## Working With Notebook\-Scoped Libraries<a name="emr-managed-notebooks-work-with-libraries"></a>

To install libraries, your Amazon EMR cluster must have access to the PyPI repository where the libraries are located\. For example, for clusters in private subnets, you may need to configure network address translation \(NAT\) and provide a path for the cluster to access the repository that is located outside the cluster’s VPC\. For more information about configuring external access for different network configurations, see [Scenarios and Examples](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenarios.html) in the *Amazon VPC User Guide*\.

By default, Python 2 is used to create the environment\. To use Python 3, you can reconfigure the notebook session by running the following commands in a notebook cell to set PySpark properties\.

```
%%configure -f
{ "conf":{
"spark.pyspark.python": "python3",
"spark.pyspark.virtualenv.enabled": "true",
"spark.pyspark.virtualenv.type":"native",
"spark.pyspark.virtualenv.bin.path":"/usr/bin/virtualenv"
}}
```

The following examples demonstrate commands to list, install, and uninstall libraries from within a notebook cell using the PySpark APIs\.

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