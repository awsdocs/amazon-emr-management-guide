# Amazon EMR Studio \(Preview\)<a name="emr-studio"></a>


|  | 
| --- |
| Amazon EMR Studio is in preview release for Amazon EMR and is subject to change\. This feature is provided as a Preview service as defined in the AWS Service Terms\. It is governed by your Agreement with AWS and the AWS Service Terms\. Before using this Preview Service, please review the terms found [here](https://aws.amazon.com/service-terms/)\. | 

With Amazon EMR 5\.32\.0 and 6\.2\.0 and later, you can use Amazon EMR Studio \(preview\) to perform data analysis and data engineering tasks in a web\-based, integrated development environment \(IDE\) using fully managed Jupyter notebooks that run on Amazon EMR clusters\. EMR Studio uses AWS Single Sign\-On \(SSO\) to let you log in to EMR Studio directly through a secure URL using your corporate credentials\.

You can accomplish the following tasks with Amazon EMR Studio:
+ Log in to Amazon EMR Studio using AWS Single Sign\-On \(SSO\) with your enterprise identity provider\.
+ Create [Workspaces](how-emr-studio-works.md#emr-studio-workspaces) with one or more notebooks\. Set up your Workspace with default options that apply to all notebooks, or customize your Workspace and cluster configurations\.
+ Access compute capacity and distributed kernels by attaching a Workspace to an EMR cluster to run Jupyter Notebook jobs\.
+ Launch Amazon EMR clusters on demand\.
+ Connect notebooks to Amazon EMR on EKS clusters to submit work as job runs\.
+ Analyze data using Python, PySpark, Spark Scala, Spark R, or SparkSQL, and install custom kernels and libraries\.
+ Collaborate with your team by linking Git repositories to share notebook code\.
+ Run parameterized notebooks as part of scheduled workflows using an orchestration tool such as Apache Airflow or Amazon Managed Workflows for Apache Airflow\.
+ Track and debug jobs using the Spark History Server, Tez UI, or YARN timeline server\. 

When you use EMR Studio during the preview period, Studios and Workspaces do not accrue additional charges\. Applicable charges for Amazon S3 storage and for Amazon EMR clusters apply when you use EMR Studio\. For more information about pricing options and details, see [Amazon EMR pricing](http://aws.amazon.com/emr/pricing/)

For information about setting up one or more EMR Studios, see [Set Up an Amazon EMR Studio for Your Team](emr-studio-set-up.md)\. To learn more about working in an EMR Studio, see [Work in an Amazon EMR Studio](work-with-an-emr-studio.md)\.

**Topics**
+ [How Amazon EMR Studio Works](how-emr-studio-works.md)
+ [Considerations and Limitations](emr-studio-considerations.md)
+ [Set Up an Amazon EMR Studio for Your Team](emr-studio-set-up.md)
+ [Work in an Amazon EMR Studio](work-with-an-emr-studio.md)