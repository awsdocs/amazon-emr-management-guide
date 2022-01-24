# Amazon EMR Studio<a name="emr-studio"></a>

Amazon EMR Studio is a web\-based integrated development environment \(IDE\) for fully managed Jupyter notebooks that run on Amazon EMR clusters\. You can set up an Amazon EMR Studio for your team to develop, visualize, and debug applications written in R, Python, Scala, and PySpark\. Amazon EMR Studio is integrated with AWS Identity and Access Management \(IAM\) and AWS SSO so users can log in using their corporate credentials\.

You can create an EMR Studio at no cost\. Applicable charges for Amazon S3 storage and for Amazon EMR clusters apply when you use EMR Studio\. For product details and highlights, see the service page for [Amazon EMR Studio](http://aws.amazon.com/emr/features/studio/)\.

## Key features of EMR Studio<a name="emr-studio-key-features"></a>

Amazon EMR Studio provides the following features:
+ Authenticate users with AWS Identity and Access Management \(IAM\) or AWS Single Sign\-On \(AWS SSO\) and your enterprise identity provider\.
+ Access and launch Amazon EMR clusters on demand to run Jupyter Notebook jobs\.
+ Connect to Amazon EMR on EKS clusters to submit work as job runs\.
+ Explore and save example notebooks\. For more information about example notebooks, see the [EMR Studio Notebook examples GitHub repository](https://github.com/aws-samples/emr-studio-notebook-examples)\.
+ Analyze data using Python, PySpark, Spark Scala, Spark R, or SparkSQL, and install custom kernels and libraries\.
+ Collaborate in real time with other users in the same Workspace\. For more information, see [Configure Workspace collaboration](emr-studio-workspace-collaboration.md)\.
+ Use the EMR Studio SQL Explorer to browse your data catalog, run SQL queries, and download results before you work with the data in a notebook\.
+ Run parameterized notebooks as part of scheduled workflows using an orchestration tool such as Apache Airflow or Amazon Managed Workflows for Apache Airflow\. For more information, see [ Orchestrating analytics jobs on EMR Notebooks using MWAA](http://aws.amazon.com/blogs/big-data/orchestrating-analytics-jobs-on-amazon-emr-notebooks-using-amazon-mwaa/) in the AWS Big Data Blog\.
+ Link code repositories such as GitHub and BitBucket\.
+ Track and debug jobs using the Spark History Server, Tez UI, or YARN timeline server\. 

EMR Studio is also HIPAA eligible and is certified under HITRUST CSF and SOC 2\. For more information about HIPAA compliance for AWS services, see [https://aws.amazon.com/compliance/hipaa-compliance/](https://aws.amazon.com/compliance/hipaa-compliance/)\. To learn more about HITRUST CSF compliance for AWS services, see [https://aws.amazon.com/compliance/hitrust/](https://aws.amazon.com/compliance/hitrust/)\. For more information about other compliance programs for AWS services, see [AWS Services in Scope by Compliance Program](https://aws.amazon.com/compliance/services-in-scope/)\.