# Prepare Input Data<a name="emr-plan-input"></a>

Most clusters load input data and then process that data\. In order to load data, it needs to be in a location that the cluster can access and in a format the cluster can process\. The most common scenario is to upload input data into Amazon S3\. Amazon EMR provides tools for your cluster to import or read data from Amazon S3\.

The default input format in Hadoop is text files, though you can customize Hadoop and use tools to import data stored in other formats\. 

**Topics**
+ [Types of Input Amazon EMR Can Accept](emr-plan-input-accept.md)
+ [How to Get Data Into Amazon EMR](emr-plan-get-data-in.md)