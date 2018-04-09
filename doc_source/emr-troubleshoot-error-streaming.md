# Streaming Cluster Errors<a name="emr-troubleshoot-error-streaming"></a>

 You can usually find the cause of a streaming error in a `syslog` file\. Link to it on the **Steps** pane\. 

The following errors are common to streaming clusters\.

**Topics**
+ [Is data being sent to the mapper in the wrong format?](#emr-troubleshoot-error-streaming-1)
+ [Is your script timing out?](#emr-troubleshoot-error-streaming-2)
+ [Are you passing in invalid streaming arguments?](#invalidarg)
+ [Did your script exit with an error?](#emr-troubleshoot-error-streaming-3)

## Is data being sent to the mapper in the wrong format?<a name="emr-troubleshoot-error-streaming-1"></a>

 To check if this is the case, look for an error message in the `syslog` file of a failed task attempt in the task attempt logs\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

## Is your script timing out?<a name="emr-troubleshoot-error-streaming-2"></a>

 The default timeout for a mapper or reducer script is 600 seconds\. If your script takes longer than this, the task attempt will fail\. You can verify this is the case by checking the `syslog` file of a failed task attempt in the task attempt logs\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

 You can change the time limit by setting a new value for the `mapred.task.timeout` configuration setting\. This setting specifies the number of milliseconds after which Amazon EMR will terminate a task that has not read input, written output, or updated its status string\. You can update this value by passing an additional streaming argument `-jobconf mapred.task.timeout=800000`\. 

## Are you passing in invalid streaming arguments?<a name="invalidarg"></a>

 Hadoop streaming supports only the following arguments\. If you pass in arguments other than those listed below, the cluster will fail\. 

```
 1. -blockAutoGenerateCacheFiles 
 2. -cacheArchive 
 3. -cacheFile 
 4. -cmdenv 
 5. -combiner 
 6. -debug 
 7. -input 
 8. -inputformat
 9. -inputreader 
10. -jobconf 
11. -mapper
12. -numReduceTasks
13. -output 
14. -outputformat 
15. -partitioner
16. -reducer
17. -verbose
```

 In addition, Hadoop streaming only recognizes arguments passed in using Java syntax; that is, preceded by a single hyphen\. If you pass in arguments preceded by a double hyphen, the cluster will fail\. 

## Did your script exit with an error?<a name="emr-troubleshoot-error-streaming-3"></a>

 If your mapper or reducer script exits with an error, you can locate the error in the `stderr` file of task attempt logs of the failed task attempt\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 