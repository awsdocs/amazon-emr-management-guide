# Considerations When Using EMR Notebooks<a name="emr-managed-notebooks-considerations"></a>

EMR Notebooks runs [Jupyter Notebook version 5\.7\.0](https://jupyter-notebook.readthedocs.io/en/stable/changelog.html#release-5-7-0) and Python 3\.6\.5\.

EMR Notebooks is pre\-configured with the following kernels and library packages installed\.

**Kernels**
+ PySpark
+ PySpark3
+ Python3
+ Spark
+ SparkR

**Library Packages**
+ [Packages for 64\-bit Linux with Python 3\.6](https://docs.anaconda.com/anaconda/packages/py3.7_linux-64/) 

If you require additional libraries that all users can access, you can install them using bootstrap actions or by specifying a custom Amazon Linux AMI for Amazon EMR when you create a cluster\. For more information, see [Create Bootstrap Actions to Install Additional Software](emr-plan-bootstrap.md) and [Using a Custom AMI](emr-custom-ami.md)\.

With Amazon EMR 5\.26\.0 and later, you can install additional library packages from within the notebook editor\. These libraries are *notebook\-scoped* libraries\. They are available only to the current notebook session\. They do not interfere with cluster\-wide libraries or libraries installed within other notebooks\. For more information, see [Using Notebook\-Scoped Libraries](emr-managed-notebooks-scoped-libraries.md)\.

Clusters that you use with an EMR notebook have requirements\. The number of notebooks that you can use with a cluster is limited by the master node configuration\. For more information, see [Creating Amazon EMR Clusters for Notebooks](emr-managed-notebooks-cluster.md)\.