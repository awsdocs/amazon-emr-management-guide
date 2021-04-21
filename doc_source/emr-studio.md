# Amazon EMR Studio<a name="emr-studio"></a>

With Amazon EMR 5\.32\.0 and 6\.2\.0 and later, you can use Amazon EMR Studio to perform data analysis and data engineering tasks in a web\-based integrated development environment \(IDE\), using fully managed Jupyter notebooks that run on Amazon EMR clusters\. Use AWS Single Sign\-On \(AWS SSO\) to sign in to EMR Studio directly through a secure URL using your corporate credentials\.

You can accomplish the following tasks with Amazon EMR Studio:
+ Log in to Amazon EMR Studio using AWS Single Sign\-On \(AWS SSO\) with your enterprise identity provider\.
+ Create [Workspaces](how-emr-studio-works.md#emr-studio-workspaces) with one or more notebooks\. Set up your Workspace with default options that apply to all notebooks, or customize your Workspace and cluster configurations\.
+ Access compute capacity and distributed kernels by attaching a Workspace to an EMR cluster to run Jupyter Notebook jobs\.
+ Launch Amazon EMR clusters on demand\.
+ Connect notebooks to Amazon EMR on EKS clusters to submit work as job runs\.
+ Explore and save example notebooks to a Workspace\. For more information about example notebooks, see the [EMR Studio Notebook Examples GitHub repository](https://github.com/aws-samples/emr-studio-notebook-examples)\.
+ Analyze data using Python, PySpark, Spark Scala, Spark R, or SparkSQL, and install custom kernels and libraries\.
+ Collaborate with your team by linking Git repositories to share notebook code\.
+ Run parameterized notebooks as part of scheduled workflows using an orchestration tool such as Apache Airflow or Amazon Managed Workflows for Apache Airflow\. For more information, see [ Orchestrating analytics jobs on Amazon EMR Notebooks using MWAA](http://aws.amazon.com/blogs/big-data/orchestrating-analytics-jobs-on-amazon-emr-notebooks-using-amazon-mwaa/) in the AWS Big Data Blog\.
+ Track and debug jobs using the Spark History Server, Tez UI, or YARN timeline server\. 

You can create an EMR Studio at no cost\. Applicable charges for Amazon S3 storage and for Amazon EMR clusters apply when you use EMR Studio\. For more information about pricing options and details, see [Amazon EMR pricing](http://aws.amazon.com/emr/pricing/)\.

For information about setting up one or more EMR Studios, see [Set Up and Manage an Amazon EMR Studio](emr-studio-set-up.md)\. To learn more about working in an EMR Studio, see [Work in an Amazon EMR Studio](work-with-an-emr-studio.md)\.

**Topics**
+ [How Amazon EMR Studio Works](how-emr-studio-works.md)
+ [Considerations and Limitations](emr-studio-considerations.md)
+ [Set Up and Manage an Amazon EMR Studio](emr-studio-set-up.md)
+ [Work in an Amazon EMR Studio](work-with-an-emr-studio.md)