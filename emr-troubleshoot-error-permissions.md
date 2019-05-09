# Permissions Errors<a name="emr-troubleshoot-error-permissions"></a>

The following errors are common when using permissions or credentials\.

**Topics**
+ [Are you passing the correct credentials into SSH?](#correctcred)
+ [If you are using IAM, do you have the proper Amazon EC2 policies set?](#check-iam-permissions)

## Are you passing the correct credentials into SSH?<a name="correctcred"></a>

 If you are unable to use SSH to connect to the master node, it is most likely an issue with your security credentials\. 

 First, check that the \.pem file containing your SSH key has the proper permissions\. You can use chmod to change the permissions on your \.pem file as is shown in the following example, where you would replace mykey\.pem with the name of your own \.pem file\. 

```
1. chmod og-rwx mykey.pem
```

 The second possibility is that you are not using the keypair you specified when you created the cluster\. This is easy to do if you have created multiple key pairs\. Check the cluster details in the Amazon EMR console \(or use the `--describe` option in the CLI\) for the name of the keypair that was specified when the cluster was created\. 

 After you have verified that you are using the correct key pair and that permissions are set correctly on the \.pem file, you can use the following command to use SSH to connect to the master node, where you would replace mykey\.pem with the name of your \.pem file and `hadoop@ec2-01-001-001-1.compute-1.amazonaws.com` with the public DNS name of the master node \(available through the `--describe` option in the CLI or through the Amazon EMR console\.\) 

**Important**  
You must use the login name `hadoop` when you connect to an Amazon EMR cluster node, otherwise an error similar to `Server refused our key` error may occur\.

```
1. ssh -i mykey.pem hadoop@ec2-01-001-001-1.compute-1.amazonaws.com				
```

 For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. 

## If you are using IAM, do you have the proper Amazon EC2 policies set?<a name="check-iam-permissions"></a>

 Because Amazon EMR uses EC2 instances as nodes, IAM users of Amazon EMR also need to have certain Amazon EC2 policies set in order for Amazon EMR to be able to manage those instances on the IAM user’s behalf\. If you do not have the required permissions set, Amazon EMR returns the error: **"User account is not authorized to call EC2\."** 

 For more information about the Amazon EC2 policies your IAM account needs to set to run Amazon EMR, see [Amazon EMR Actions in User\-Based IAM Policies](emr-plan-access-iam.md#emr-set-iam-policy)\. 