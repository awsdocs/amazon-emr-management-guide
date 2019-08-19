# Using Notebooks with Lake Formation<a name="emr-lf-notebook"></a>

Both Apache Zeppelin and EMR Notebooks are integrated with Lake Formation, and can be used once an EMR cluster integrated with Lake Formation has been created\. 

In order to access both notebook applications, you must first ensure that your cluster’s EC2 security group or VPC network access control list \(ACL\) is configured to allow access to the Proxy Agent \(port 8442\) from your desktop\.

**Note**  
The Proxy Agent on the EMR cluster uses a self\-signed Transport Layer Security \(TLS\) certificate by default, and your browser will prompt you to accept the certificate before proceeding\. If you want to use a custom certificate for the Proxy Agent, see the “Customize Proxy Agent Certificate” section\.

## Apache Zeppelin<a name="emr-lf-notebook-zeppelin"></a>

To access Apache Zeppelin, use the EMR console to locate the *Master public DNS* from the cluster’s **Summary** tab\. Using your browser, navigate to https://*MasterPublicDNS*:8442/gateway/default/zeppelin/\. Ensure the URL includes the trailing slash at the end\. 

Once the Proxy Agent’s certificate is accepted, your browser redirects you to your Identity Provider \(IdP\) to authenticate\. Once authenticated, you will be redirected to Zeppelin\. 

**Creating your first Zeppelin Notebook**

To get started, create a new Notebook by selecting **Notebook**, **Create new note**\. Give your Notebook a name and use the default **livy** interpreter\.

To see a list of Lake Formation databases, use the following Spark SQL command\.

```
spark.sql("show databases").show()
```

To query a specific Lake Formation table, use the following Spark SQL command\. Replace *database\.table* with actual databases and tables in Lake Formation:

```
spark.sql("SELECT * FROM database.table limit 10").show()
```

## EMR Notebooks<a name="emr-lf-notebook-emr"></a>

EMR notebooks can be created using the Amazon EMR console and used with an existing EMR cluster integrated with Lake Formation\.

**To create an EMR notebook**

1. Open the Amazon EMR console at https://console\.aws\.amazon\.com/elasticmapreduce/\.

1. Choose **Notebooks**, **Create notebook**\.

1. Enter a **Notebook name** and an optional **Notebook description**\.

1. Select **Choose an existing cluster**, and then **Choose**\.

1. Select an existing EMR cluster integrated with Lake Formation\.

1. Select **Create notebook** to create the notebook\.

Once the Notebook has been created, select the notebook and click **Open**\. You will be redirected to the Proxy Agent on the Amazon EMR cluster\. Once you’ve accepted the Proxy Agent’s certificate, your browser will redirect you to your Identity Provider \(IdP\) to authenticate\. Once authenticated, you will be redirected to the EMR notebook\.

For more information, see [Using Amazon EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebook.html) in the *Amazon EMR Management Guide*\.