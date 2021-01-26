# Fetch Your EMR Studio ID<a name="emr-studio-get-studio-id"></a>

To retrieve a detailed list of your EMR Studios with information such as Studio name, ID, and access URL, use the following `list-studios` AWS CLI command\.

```
aws emr list-studios
```

The `list-studios` command returns a list of EMR Studios similar to the following output\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/emr/list-studios.html](https://docs.aws.amazon.com/cli/latest/reference/emr/list-studios.html)\.

```
{
    "Studios": [
        {
            "VpcId": "vpc-b21XXXXX", 
            "Name": "example-studio-name", 
            "Url": "https://es-7HWP74SNGDXXXXXXXXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com (https://es-7hwp74sngdXXXXXXXXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com/)", 
            "CreationTime": 1605672582.781, 
            "StudioId": "es-7HWP74SNGDXXXXXXXXXXXXXXX", 
            "Description": "example studio description"
        }
    ]
}
```