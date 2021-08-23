# Optimize Spark jobs in EMR Studio<a name="emr-studio-spark-optimization"></a>

When running a Spark job using EMR Studio, there are a few steps you can take to help ensure that you're optimizing your Amazon EMR cluster resources\.

## Prolong your Livy session<a name="optimize-spark-livy-timeout"></a>

If you use Apache Livy along with Spark on your Amazon EMR cluster, we recommend that you increase your Livy session timeout by doing one of the following:
+ When you create an Amazon EMR cluster, set this configuration classification in the **Enter Configuration** field\.

  ```
  [
      {
          "Classification": "livy-conf",
          "Properties": {
            "livy.server.session.timeout": "8h"
          }
      }
  ]
  ```
+ For an already\-running EMR cluster, connect to your cluster using `ssh` and set the `livy-conf` configuration classification in `/etc/livy/conf/livy.conf`\.

  ```
  [
      {
          "Classification": "livy-conf",
          "Properties": {
            "livy.server.session.timeout": "8h"
          }
      }
  ]
  ```

  You may need to restart Livy after changing the configuration\.
+ If you don't want your Livy session to time out at all, set the property `livy.server.session.timeout-check` to `false` in `/etc/livy/conf/livy.conf`\.

## Run Spark in cluster mode<a name="optimize-spark-cluster-mode"></a>

In cluster mode, the Spark driver runs on a core node instead of on the master node, improving resource utilization on the master node\. 

To run your Spark application in cluster mode instead of the default client mode, choose **Cluster** mode when you set **Deploy mode** while configuring your Spark step in your new Amazon EMR cluster\. For more information, see [Cluster mode overview](https://spark.apache.org/docs/latest/cluster-overview.html) in the Apache Spark documentation\.

## Increase Spark driver memory<a name="optimize-spark-memory"></a>

To increase the Spark driver memory, configure your Spark session using the `%%configure` magic command in your EMR notebook, as in the following example\.

```
%%configure -f
{"driverMemory": "6000M"}
```