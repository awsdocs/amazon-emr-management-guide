# Customize Proxy Agent Certificate<a name="emr-lf-TLS"></a>

The proxy agent uses a self\-signed Transport Layer Security \(TLS\) certificate by default\. To use a custom certificate for the proxy agent, you must first obtain a certificate, certificate chain, and private key from your certificate authority\. With those items, a PKCS12 file will be used to protect the key material so it can be imported into the proxy agent’s key store\. The proxy agent is based on Apache Knox\. You can replace the default certificate with your custom certificate using the following steps\. 

In the following steps, replace *MasterPublicDNS* with the value that appears for **Master public DNS** on the **Summary** tab of the **cluster details** pane\. For example, *ec2\-11\-222\-33\-44\.compute\-1\.amazonaws\.com*\.

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