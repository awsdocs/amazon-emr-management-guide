# Use multi\-language notebooks with Spark kernels<a name="emr-multi-language-kernels"></a>

Each Jupyter notebook kernel has a default language\. For example, the Spark kernel’s default language is Scala, and the PySpark kernels’s default language is Python\. With Amazon EMR 6\.4\.0 and later, EMR Studio supports multi\-language notebooks\. This means that each kernel in EMR Studio can support the following languages in addition to the default language: Python, Spark, R, and Spark SQL\.

To activate this feature, specify one of the following magic commands at the beginning of any cell\.


****  

| Language | Command | 
| --- | --- | 
| Python | %%pyspark | 
| Scala | %%scalaspark | 
| R | %%rspark | 
| Spark SQL | %%sql | 

When invoked, these commands execute the entire cell within the same Spark session using the interpreter of the corresponding language\.

The `%%pyspark` cell magic allows users to write PySpark code in all Spark kernels\.

```
%%pyspark
a = 1
```

The `%%sql` cell magic allows users to execute Spark\-SQL code in all Spark kernels\.

```
%%sql
SHOW TABLES
```

The `%%rspark` cell magic allows users to execute SparkR code in all Spark kernels\.

```
%%rspark
a <- 1
```

The `%%scalaspark` cell magic allows users to execute Spark Scala code in all Spark kernels\.

```
%%scalaspark
val a = 1
```

## Share data across language interpreters with temporary tables<a name="emr-temp-tables"></a>

You can also share data between language interpreters using temporary tables\. The following example uses `%%pyspark` in one cell to create a temporary table in Python and uses `%%scalaspark` in the following cell to read data from that table in Scala\.

```
%%pyspark
df=spark.sql("SELECT * from nyc_top_trips_report LIMIT 20")
# create a temporary table called nyc_top_trips_report_view in python
df.createOrReplaceTempView("nyc_top_trips_report_view")
```

```
%%scalaspark
// read the temp table in scala
val df=spark.sql("SELECT * from nyc_top_trips_report_view")
df.show(5)
```