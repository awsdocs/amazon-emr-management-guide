# Sample commands to execute EMR Notebooks programmatically<a name="emr-managed-notebooks-headless"></a>

EMR Notebook execution APIs are available to execute EMR notebooks via a script or command line\. The ability to start, stop, list, and describe EMR notebook executions without the AWS console enables you programmatically control an EMR notebook\. Using a parameterized notebook cell, you can pass different parameter values to a notebook without having to create a copy of the notebook for each new set of parameter values\. See [EMR API actions](https://docs.aws.amazon.com/emr/latest/APIReference/API_Operations.html)\.

EMR notebook execution can be scheduled or batched using Amazon CloudWatch events and AWS Lambda\. See [Using AWS Lambda with Amazon CloudWatch Events](https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html)

This section provides several examples of programmatic EMR notebook execution using the AWS CLI, Boto3 SDK \(Python\), and Ruby\.

[Notebook execution CLI command samples](emr-managed-notebooks-headless-cli.md)

[Notebook execution Python samples](emr-managed-notebooks-headless-python.md)

[Notebook execution Ruby samples](emr-managed-notebooks-headless-ruby.md)

You can also run parameterized notebooks as part of scheduled workflows using an orchestration tool such as Apache Airflow or Amazon Managed Workflows for Apache Airflow\. For more information, see [ Orchestrating analytics jobs on EMR Notebooks using MWAA](http://aws.amazon.com/blogs/big-data/orchestrating-analytics-jobs-on-amazon-emr-notebooks-using-amazon-mwaa/) in the AWS Big Data Blog\.

**Limitations:**
+ A maximum of 100 concurrent executions are allowed per region per account\.
+ An execution is terminated if running for more than 30 days\.