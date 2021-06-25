# Set up EMR Notebooks to access Lake Formation data<a name="emr-lf-notebook"></a>

After you opt in to data filtering on Amazon EMR and create a cluster integrated with Lake Formation, you can use both Apache Zeppelin and EMR Notebooks to access data\. 

In order to access both notebook applications, you must first ensure that your cluster's EC2 security group or VPC network access control list \(ACL\) is configured to allow access to the Proxy Agent \(port 8442\) from your desktop\.

**Note**  
The Proxy Agent on the EMR cluster uses a self\-signed Transport Layer Security \(TLS\) certificate by default, and your browser will prompt you to accept the certificate before proceeding\. If you want to use a custom certificate for the Proxy Agent, see the "Customize Proxy Agent Certificate" section\.

## Apache Zeppelin<a name="emr-lf-notebook-zeppelin"></a>

To access Apache Zeppelin, use the EMR console to locate the *Master public DNS* from the cluster's **Summary** tab\. Using your browser, navigate to https://*MasterPublicDNS*:8442/gateway/default/zeppelin/\. Ensure the URL includes the trailing slash at the end\. 

Once the Proxy Agent's certificate is accepted, your browser redirects you to your Identity Provider \(IdP\) to authenticate\. Once authenticated, you will be redirected to Zeppelin\. 

**Creating your first Zeppelin Notebook**

To get started, create a new Notebook by selecting **Notebook**, **Create new note**\. Give your Notebook a name and use the default **livy** interpreter\.

To see a list of Lake Formation databases, use the following Spark SQL command\.

```
spark.sql("show databases").show()
```

To query a specific Lake Formation table, use the following Spark SQL command\. Replace *database\.table* with actual databases and tables in Lake Formation:

```
spark.sql("SELECT * FROM database.table limit 10").show()
```

## EMR Notebooks<a name="emr-lf-notebook-emr"></a>

EMR Notebooks can be created using the Amazon EMR console and used with an existing EMR cluster integrated with Lake Formation\.

**To create an EMR notebook**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce)\.

1. Choose **Notebooks**, **Create notebook**\.

1. Enter a **Notebook name** and an optional **Notebook description**\.

1. Select **Choose an existing cluster**, and then **Choose**\.

1. Select an existing EMR cluster integrated with Lake Formation\.

1. Select **Create notebook** to create the notebook\.

Once the Notebook has been created, select the notebook and click **Open**\. You will be redirected to the Proxy Agent on the Amazon EMR cluster\. Once you've accepted the Proxy Agent's certificate, your browser will redirect you to your Identity Provider \(IdP\) to authenticate\. Once authenticated, you will be redirected to the EMR notebook\.

For more information, see [Using Amazon EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks.html) in the *Amazon EMR Management Guide*\.

## Customize your proxy agent certificate<a name="emr-lf-TLS"></a>

The proxy agent uses a self\-signed Transport Layer Security \(TLS\) certificate by default\. You must replace the default certificate with a custom certificate for the proxy agent\. To do that, you must first obtain a certificate, certificate chain, and private key from your certificate authority\. With those items, a PKCS12 file will be used to protect the key material so it can be imported into the proxy agent's key store\. The proxy agent is based on Apache Knox\. 

Take the following steps to replace the default certificate with your custom certificate\. Replace *MasterPublicDNS* with the value that appears for **Master public DNS** on the **Summary** tab of the **cluster details** pane\. For example, *ec2\-11\-222\-33\-44\.compute\-1\.amazonaws\.com*\.

1. To create a PKCS12 file from your certificate, certificate chain, and private key, run the following command on a host with the certificate files, and `openssl` installed\.

   ```
   openssl pkcs12 -export -out proxy_agent_certificate.pfx -inkey private.key -in certificate.cer -certfile certchain.cer
   ```

1. Copy the `proxy_agent_certificate.pfx` file to the `/home/hadoop` directory on the master node of your cluster\.

   ```
   scp -i EC2KeyPair.pem proxy_agent_certificate.pfx hadoop@MasterPublicDNS:/home/hadoop
   ```

1. SSH into the master node of your cluster\.

   ```
   ssh -i EC2KeyPair.pem hadoop@MasterPublicDNS
   ```

1. Find your cluster\-specific master key by using the following command\.

   ```
   less /etc/knox/conf/gateway-site.xml
   ```

   Look for the *gateway\.master\.secret* property and copy the contents of the `value` tag, as you will need it for future steps\.

1. Create a backup copy of the existing proxy agent keystores by using the following commands\.

   ```
   sudo -s
   cd /mnt/var/lib/knox/data/security/keystores
   mkdir backups
   mv gateway.jks __gateway-credentials.jceks backups/
   ```

1. Import your custom certificates into a new keystore by using the following commands\.

   ```
   sudo -s
   cd /mnt/var/lib/knox/data/security/keystores
   keytool -importkeystore \
   -srckeystore /home/hadoop/proxy_agent_certificate.pfx \
   -srcstoretype pkcs12 -destkeystore gateway.jks \
   -deststoretype jks \
   -srcalias 1 \
   -destalias gateway-identity
   ```

   When prompted for the `Enter destination keystore password`, use the Knox master secret from the `gateway-site.xml` file\.

   Ensure the newly created `gateway.jks` file is owned by the knox user by using the following command\.

   ```
   chown knox:knox gateway.jks
   ```

   If your private key is protected by a password, make sure Knox is aware of that password\.

   ```
   sudo -u knox bash
   cd /usr/lib/knox
   bin/knoxcli.sh create-cert create-alias gateway-identity-passphrase
   ```

   Enter the password protecting your private key when prompted\.

1. Restart Knox by using the following commands\.

   ```
   sudo -u knox bash
   cd /usr/lib/knox
   bin/gateway.sh stop
   ```

   Knox should be restarted automatically, and you can check the status of Knox by viewing the `/var/log/knox/gateway.log`\.

1. To ensure the new certificate is being used by the proxy agent, navigate to Apache Zeppelin https://*MasterPublicDNS*:8442/gateway/default/zeppelin/\. You can use your browser to inspect the certificate to ensure it is your custom certificate\.