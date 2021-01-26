# Delete an Amazon EMR Studio<a name="emr-studio-delete-studio"></a>

Use the `delete-studio` AWS CLI command to delete an Amazon EMR Studio\. Replace `<es-7YGXXXXXXXXXXXXXXXXXXXXXX>` with the ID of the Studio that you want to delete\.

```
aws emr delete-studio --studio-id <es-7YGXXXXXXXXXXXXXXXXXXXXXX>
```

When you delete a Studio, EMR Studio deletes all of the user and group assignments associated with the Studio\. You might need to take extra steps to remove the notebook files associated with the Studio from Amazon S3\. For more information, see [Delete a Workspace and Notebook Files](emr-studio-configure-workspace.md#emr-studio-delete-workspace)\.