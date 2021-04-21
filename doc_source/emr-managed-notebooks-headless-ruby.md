# Notebook execution Ruby samples<a name="emr-managed-notebooks-headless-ruby"></a>

The following are Ruby code samples that demonstrate using the notebook execution API\.

```
# prepare an EMR client

emr = Aws::EMR::Client.new(
  region: 'us-east-1',
  access_key_id: 'AKIA...JKPKA',
  secret_access_key: 'rLMeu...vU0OLrAC1',
)
```

## Starting notebook execution and getting the execution id<a name="emr-managed-notebooks-headless-ruby-startretrieve"></a>

In this example, the S3 editor and EMR notebook are `s3://mybucket/notebooks/e-EA8VGAA429FEQTC8HC9ZHWISK/test.ipynb`\.

For information about the EMR API NotebookExecution actions, see [EMR API Actions\.](https://docs.aws.amazon.com/emr/latest/APIReference/API_Operations.html)

```
start_response = emr.start_notebook_execution({
    editor_id: "e-EA8VGAA429FEQTC8HC9ZHWISK",
    relative_path: "test.ipynb",
    
    execution_engine: {id: "j-3U82I95AMALGE"},
    
    service_role: "EMR_Notebooks_DefaultRole",
})


notebook_execution_id = start_resp.notebook_execution_id
```

## Describing notebook execution and printing the details<a name="emr-managed-notebooks-headless-ruby-describeprint"></a>

```
describe_resp = emr.describe_notebook_execution({
    notebook_execution_id: notebook_execution_id
})
puts describe_resp.notebook_execution

{
:notebook_execution_id=>"ex-IZX3VTVZWVWPP27KUB90BZ7V9IEDG", 
:editor_id=>"e-EA8VGAA429FEQTC8HC9ZHWISK",
:execution_engine=>{:id=>"j-3U82I95AMALGE", :type=>"EMR", :master_instance_security_group_id=>nil}, 
:notebook_execution_name=>"", 
:notebook_params=>nil, 
:status=>"STARTING", 
:start_time=>2020-07-23 15:07:07 -0700, 
:end_time=>nil, 
:arn=>"arn:aws:elasticmapreduce:us-east-1:123456789012:notebook-execution/ex-IZX3VTVZWVWPP27KUB90BZ7V9IEDG", 
:output_notebook_uri=>nil, 
:last_state_change_reason=>"Execution is starting for cluster j-3U82I95AMALGE.", :notebook_instance_security_group_id=>nil, 
:tags=>[]
}
```

## Listing notebook executions<a name="emr-managed-notebooks-headless-ruby-listexe"></a>

```
puts 'Listing executions:'
list_resp = emr.list_notebook_executions({})
puts list_resp
```

## Notebook filters<a name="emr-managed-notebooks-headless-ruby-filters"></a>

```
"EditorId": "e-XXXX",           [Optional]
"From" : "1593400000.000",    [Optional]
"To" : "1598400000.500",      [Optional] 
"Status":                       [Optional]
        "START_PENDING | STARTING | RUNNING | STOP_PENDING | STOPPING | FINISHING | FAILING | FINSHED | FAILED | STOPPED"
```

An EMR notebook can be stopped when it is in one of these states: `START_PENDING, STARTING, RUNNING`\.

### Stopping notebook execution<a name="emr-managed-notebooks-headless-ruby-stop"></a>

```
stop_resp = emr.stop_notebook_execution({
    notebook_execution_id: notebook_execution_id
})
```

### Scheduling notebook executions<a name="emr-managed-notebooks-headless-ruby-cron"></a>

EMR notebooks can be scheduled using either:
+ A cron job with bash script
+ An Amazon CloudWatch event, which will trigger an AWS Lambda \(Java/Python/Javascript\)\. For more information, see [Using AWS Lambda with Amazon CloudWatch Events](https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html)\.

```
#!/bin/bash
aws emr --region us-east-1 \
start-notebook-execution \
--editor-id e-EA8VGAA429FEQTC8HC9ZHWISK \
--notebook-params '{"input_param":"my-value", "good_superhero":["superman", "batman"], "new_param":{"nest-key1":"nest-val1", "nest-key2":"nest-val2"}}' \
--relative-path test.ipynb \
--notebook-execution-name daily-job \
--execution-engine '{"Id" : "j-3U82I95AMALGE"}' \
--service-role EMR_Notebooks_DefaultRole
```