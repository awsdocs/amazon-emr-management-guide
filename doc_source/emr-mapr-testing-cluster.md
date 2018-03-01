# Testing Your Cluster<a name="emr-mapr-testing-cluster"></a>

This section explains how to test your cluster by performing a word count on a sample input file\. 

**To create a file and run a test job**

1. Connect to the master node with SSH as user hadoop\. Pass your \.pem credentials file to ssh with the \-i flag, as in this example: 

   ```
   ssh -i /path_to_pemfile/credentials.pem hadoop@masterDNS.amazonaws.com
   ```

1. Create a simple text file: 

   ```
   cd /mapr/MapR_EMR.amazonaws.com
   mkdir in
   echo "the quick brown fox jumps over the lazy dog" > in/data.txt
   ```

1. Run the following command to perform a word count on the text file: 

   ```
   hadoop jar /opt/mapr/hadoop/hadoop-0.20.2/hadoop-0.20.2-dev-examples.jar wordcount /mapr/MapR_EMR.amazonaws.com/in/ /mapr/MapR_EMR.amazonaws.com/out/
   ```

    As the job runs, you should see terminal output similar to the following: 

   ```
   12/06/09 00:00:37 INFO fs.JobTrackerWatcher: Current running JobTracker is: ip10118194139.ec2.internal/10.118.194.139:9001
   12/06/09 00:00:37 INFO input.FileInputFormat: Total input paths to process : 1
   12/06/09 00:00:37 INFO mapred.JobClient: Running job: job_201206082332_0004
   12/06/09 00:00:38 INFO mapred.JobClient: map 0% reduce 0%
   12/06/09 00:00:50 INFO mapred.JobClient: map 100% reduce 0%
   12/06/09 00:00:57 INFO mapred.JobClient: map 100% reduce 100%
   12/06/09 00:00:58 INFO mapred.JobClient: Job complete: job_201206082332_0004
   12/06/09 00:00:58 INFO mapred.JobClient: Counters: 25
   12/06/09 00:00:58 INFO mapred.JobClient: Job Counters
   12/06/09 00:00:58 INFO mapred.JobClient: Launched reduce tasks=1
   12/06/09 00:00:58 INFO mapred.JobClient: Aggregate execution time of mappers(ms)=6193
   12/06/09 00:00:58 INFO mapred.JobClient: Total time spent by all reduces waiting after reserving slots (ms)=0
   12/06/09 00:00:58 INFO mapred.JobClient: Total time spent by all maps waiting after reserving slots (ms)=0
   12/06/09 00:00:58 INFO mapred.JobClient: Launched map tasks=1
   12/06/09 00:00:58 INFO mapred.JobClient: Datalocalmap tasks=1
   12/06/09 00:00:58 INFO mapred.JobClient: Aggregate execution time of reducers(ms)=4875
   12/06/09 00:00:58 INFO mapred.JobClient: FileSystemCounters
   12/06/09 00:00:58 INFO mapred.JobClient: MAPRFS_BYTES_READ=385
   12/06/09 00:00:58 INFO mapred.JobClient: MAPRFS_BYTES_WRITTEN=276
   12/06/09 00:00:58 INFO mapred.JobClient: FILE_BYTES_WRITTEN=94449
   12/06/09 00:00:58 INFO mapred.JobClient: MapReduce Framework
   12/06/09 00:00:58 INFO mapred.JobClient: Map input records=1
   12/06/09 00:00:58 INFO mapred.JobClient: Reduce shuffle bytes=94
   12/06/09 00:00:58 INFO mapred.JobClient: Spilled Records=16
   12/06/09 00:00:58 INFO mapred.JobClient: Map output bytes=80
   12/06/09 00:00:58 INFO mapred.JobClient: CPU_MILLISECONDS=1530
   12/06/09 00:00:58 INFO mapred.JobClient: Combine input records=9
   12/06/09 00:00:58 INFO mapred.JobClient: SPLIT_RAW_BYTES=125
   12/06/09 00:00:58 INFO mapred.JobClient: Reduce input records=8
   12/06/09 00:00:58 INFO mapred.JobClient: Reduce input groups=8
   12/06/09 00:00:58 INFO mapred.JobClient: Combine output records=8
   12/06/09 00:00:58 INFO mapred.JobClient: PHYSICAL_MEMORY_BYTES=329244672
   12/06/09 00:00:58 INFO mapred.JobClient: Reduce output records=8
   12/06/09 00:00:58 INFO mapred.JobClient: VIRTUAL_MEMORY_BYTES=3252969472
   12/06/09 00:00:58 INFO mapred.JobClient: Map output records=9
   12/06/09 00:00:58 INFO mapred.JobClient: GC time elapsed (ms)=1
   ```

1. Check the /mapr/MapR\_EMR\.amazonaws\.com/out directory for a file named `part-r-00000` with the results of the job\. 

   ```
   cat out/partr00000
   brown 1
   dog 1
   fox 1
   jumps 1
   lazy 1
   over 1
   quick 1
   the 2
   ```