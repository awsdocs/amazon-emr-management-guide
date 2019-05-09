# Custom JAR Cluster Errors<a name="emr-troubleshoot-error-custom-jar"></a>

The following errors are common to custom JAR clusters\.

**Topics**
+ [Is your JAR throwing an exception before creating a job?](#emr-troubleshoot-error-custom-jar-1)
+ [Is your JAR throwing an error inside a map task?](#emr-troubleshoot-error-custom-jar-2)

## Is your JAR throwing an exception before creating a job?<a name="emr-troubleshoot-error-custom-jar-1"></a>

 If the main program of your custom JAR throws an exception while creating the Hadoop job, the best place to look is the `syslog` file of the step logs\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 

## Is your JAR throwing an error inside a map task?<a name="emr-troubleshoot-error-custom-jar-2"></a>

 If your custom JAR and mapper throw an exception while processing input data, the best place to look is the `syslog` file of the task attempt logs\. For more information, see [View Log Files](emr-manage-view-web-log-files.md)\. 