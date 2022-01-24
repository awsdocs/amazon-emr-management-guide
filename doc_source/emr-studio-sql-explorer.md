# Browse data with SQL Explorer<a name="emr-studio-sql-explorer"></a>

This topic provides information to help you get started with Amazon EMR Studio's SQL Explorer\. SQL Explorer is a single\-page tool in your Workspace that helps you understand the data sources in your EMR cluster's data catalog\. You can use SQL Explorer to browse your data, run SQL queries to retrieve data, and download query results\.

SQL Explorer supports Presto\. Before you use SQL Explorer, make sure you have an EMR cluster that uses Amazon EMR version 6\.4\.0 or later with Presto installed\.

## Browse your cluster's data catalog<a name="emr-studio-sql-explorer-browse"></a>

SQL Explorer provides a catalog browser interface that you can use to explore and understand how your data is organized\. For example, you can use the data catalog browser to verify table and column names before you write a SQL query\.

**To browse your data catalog**

1. Open SQL Explorer in your Workspace\.

1. Make sure your Workspace is attached to an EMR cluster running on EC2 that uses Amazon EMR version 6\.4\.0 or later with Presto installed\. You can choose an existing cluster, or create a new one\. For more information, see [Attach a cluster to a Workspace](emr-studio-create-use-clusters.md)\.

1. Select a **Database** from the dropdown list to browse\.

1. Expand a table in your database to see the table's column names\. You can also enter a keyword in the search bar to filter table results\.

## Run a SQL query to retrieve data<a name="emr-studio-sql-explorer-run-query"></a>

**To retrieve data with a SQL query and download the results**

1. Open SQL Explorer in your Workspace\.

1. Make sure your Workspace is attached to an EMR cluster running on EC2 with Presto and Spark installed\. You can choose an existing cluster, or create a new one\. For more information, see [Attach a cluster to a Workspace](emr-studio-create-use-clusters.md)\.

1. Select **Open editor** to open a new editor tab in your Workspace\.

1. Compose your SQL query in the editor tab\.

1. Choose **Run**\.

1. View your query results under **Result preview**\. SQL Explorer displays the first 100 results by default\. You can choose a different number of results to display \(up to 1000\) using the **Preview first 100 query results** drowdown\.

1. Choose **Download results** to download your results in CSV format\. You can download up to 1000 rows of results\.