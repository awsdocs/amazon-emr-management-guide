# Notebook execution Python samples<a name="emr-managed-notebooks-headless-python"></a>

The following code example is a Boto3 \(Python AWS SDK\) file called `demo.py` that shows the notebook execution APIs\.

For information about the EMR API NotebookExecution actions, see [EMR API Actions\.](https://docs.aws.amazon.com/emr/latest/APIReference/API_Operations.html)

```
    demo.py:
     
    import boto3,time
     
    emr = boto3.client(
        'emr',
        region_name='us-west-1'
    )
     
     
     
    start_resp = emr.start_notebook_execution(
    	EditorId='e-40AC8ZO6EGGCPJ4DLO48KGGGI',
    	RelativePath='boto3_demo.ipynb',
    	ExecutionEngine={'Id':'j-1HYZS6JQKV11Q'},
    	ServiceRole='EMR_Notebooks_DefaultRole'
    )
     
    execution_id = start_resp["NotebookExecutionId"]
    print(execution_id)
    print("\n")
     
     
    describe_response = emr.describe_notebook_execution(NotebookExecutionId=execution_id)
     
    print(describe_response)
    print("\n")
     
     
     
    list_response = emr.list_notebook_executions()
    print("Existing notebook executions:\n")
    for execution in list_response['NotebookExecutions']:
    	print(execution)
    	print("\n")
     
     
     
    print("Sleeping for 5 sec...")
    time.sleep(5)
     
    print("Stop execution " + execution_id)
    emr.stop_notebook_execution(NotebookExecutionId=execution_id)
    describe_response = emr.describe_notebook_execution(NotebookExecutionId=execution_id)
    print(describe_response)
    print("\n")
```

Here's the output from running demo\.py\.

```
     
    [ec2-user@ip-10-1-58-123 ~]$ python demo.py
    ex-IZX56YJDW1D29Q1PHR32WABU2SAPK
     
     
    {'NotebookExecution': {'NotebookExecutionId': 'ex-IZX56YJDW1D29Q1PHR32WABU2SAPK', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'ExecutionEngine': {'Id': 'j-1HYZS6JQKV11Q', 'Type': 'EMR'}, 'NotebookExecutionName': '', 'Status': 'STARTING', 'StartTime': datetime.datetime(2020, 8, 19, 0, 49, 19, 418000, tzinfo=tzlocal()), 'Arn': 'arn:aws:elasticmapreduce:us-west-1:123456789012:notebook-execution/ex-IZX56YJDW1D29Q1PHR32WABU2SAPK', 'LastStateChangeReason': 'Execution is starting for cluster j-1HYZS6JQKV11Q.', 'Tags': []}, 'ResponseMetadata': {'RequestId': '70f12c5f-1dda-45b7-adf6-964987d373b7', 'HTTPStatusCode': 200, 'HTTPHeaders': {'x-amzn-requestid': '70f12c5f-1dda-45b7-adf6-964987d373b7', 'content-type': 'application/x-amz-json-1.1', 'content-length': '448', 'date': 'Wed, 19 Aug 2020 00:49:22 GMT'}, 'RetryAttempts': 0}}
     
     
    Existing notebook executions:
     
    {'NotebookExecutionId': 'ex-IZX56YJDW1D29Q1PHR32WABU2SAPK', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'STARTING', 'StartTime': datetime.datetime(2020, 8, 19, 0, 49, 19, 418000, tzinfo=tzlocal())}
     
     
    {'NotebookExecutionId': 'ex-IZX5ABS5PR1E5AHMFYEMX3JJIORRB', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'RUNNING', 'StartTime': datetime.datetime(2020, 8, 19, 0, 48, 36, 373000, tzinfo=tzlocal())}
     
     
    {'NotebookExecutionId': 'ex-IZX5GLVXIU1HNI8BWVW057F6MF4VE', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'FINISHED', 'StartTime': datetime.datetime(2020, 8, 19, 0, 45, 14, 646000, tzinfo=tzlocal()), 'EndTime': datetime.datetime(2020, 8, 19, 0, 46, 26, 543000, tzinfo=tzlocal())}
     
     
    {'NotebookExecutionId': 'ex-IZX5CV8YDUO8JAIWMXN2VH32RUIT1', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'FINISHED', 'StartTime': datetime.datetime(2020, 8, 19, 0, 43, 5, 807000, tzinfo=tzlocal()), 'EndTime': datetime.datetime(2020, 8, 19, 0, 44, 31, 632000, tzinfo=tzlocal())}
     
     
    {'NotebookExecutionId': 'ex-IZX5AS0PPW55CEDEURZ9NSOWSUJZ6', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'FINISHED', 'StartTime': datetime.datetime(2020, 8, 19, 0, 42, 29, 265000, tzinfo=tzlocal()), 'EndTime': datetime.datetime(2020, 8, 19, 0, 43, 48, 320000, tzinfo=tzlocal())}
     
     
    {'NotebookExecutionId': 'ex-IZX57YF5Q53BKWLR4I5QZ14HJ7DRS', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'NotebookExecutionName': '', 'Status': 'FINISHED', 'StartTime': datetime.datetime(2020, 8, 19, 0, 38, 37, 81000, tzinfo=tzlocal()), 'EndTime': datetime.datetime(2020, 8, 19, 0, 40, 39, 646000, tzinfo=tzlocal())}
     
     
    Sleeping for 5 sec...
    Stop execution ex-IZX56YJDW1D29Q1PHR32WABU2SAPK
    {'NotebookExecution': {'NotebookExecutionId': 'ex-IZX56YJDW1D29Q1PHR32WABU2SAPK', 'EditorId': 'e-40AC8ZO6EGGCPJ4DLO48KGGGI', 'ExecutionEngine': {'Id': 'j-1HYZS6JQKV11Q', 'Type': 'EMR'}, 'NotebookExecutionName': '', 'Status': 'STOPPING', 'StartTime': datetime.datetime(2020, 8, 19, 0, 49, 19, 418000, tzinfo=tzlocal()), 'Arn': 'arn:aws:elasticmapreduce:us-west-1:123456789012:notebook-execution/ex-IZX56YJDW1D29Q1PHR32WABU2SAPK', 'LastStateChangeReason': 'Execution is being stopped for cluster j-1HYZS6JQKV11Q.', 'Tags': []}, 'ResponseMetadata': {'RequestId': '2a77ef73-c1c6-467c-a1d1-7204ab2f6a53', 'HTTPStatusCode': 200, 'HTTPHeaders': {'x-amzn-requestid': '2a77ef73-c1c6-467c-a1d1-7204ab2f6a53', 'content-type': 'application/x-amz-json-1.1', 'content-length': '453', 'date': 'Wed, 19 Aug 2020 00:49:30 GMT'}, 'RetryAttempts': 0}}
```