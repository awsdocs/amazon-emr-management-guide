# TLS certificates<a name="emr-ranger-admin-tls"></a>

Apache Ranger integration with Amazon EMR requires that traffic from Amazon EMR nodes to the Ranger Admin server is encrypted using TLS, and that Ranger plugins authenticate to the Apache Ranger server using two\-way mutual TLS authentication\. Amazon EMR service needs the public certificate of your Ranger Admin server \(specified in the previous example\) and the private certificate\.

**Apache Ranger plugin certificates**

Apache Ranger plugin public TLS certificates must be accessible to the Apache Ranger Admin server to validate when the plugins connect\. There are three different methods to do this\.

**Method 1: Configure a truststore in Apache Ranger Admin server**

Fill in the following configurations in ranger\-admin\-site\.xml to configure a truststore\.

```
<property>
    <name>ranger.truststore.file</name>
    <value><LOCATION TO TRUSTSTORE></value>
</property>

<property>
    <name>ranger.truststore.password</name>
    <value><PASSWORD FOR TRUSTSTORE></value>
</property>
```

**Method 2: Load the certificate into Java cacerts truststore**

If your Ranger Admin server doesn't specify a truststore in its JVM options, then you can put the plugin public certificates in the default cacerts store\.

**Method 3: Create a truststore and specify as part of JVM Options**

Within `{RANGER_HOME_DIRECTORY}/ews/ranger-admin-services.sh`, modify `JAVA_OPTS` to include `"-Djavax.net.ssl.trustStore=<TRUSTSTORE_LOCATION>"` and `"-Djavax.net.ssl.trustStorePassword=<TRUSTSTORE_PASSWORD>"`\. For example, add the following line after the existing JAVA\_OPTS\.

```
JAVA_OPTS=" ${JAVA_OPTS} -Djavax.net.ssl.trustStore=${RANGER_HOME}/truststore/truststore.jck -Djavax.net.ssl.trustStorePassword=changeit"
```

**Note**  
This specification may expose the truststore password if any user is able to log into the Apache Ranger Admin server and see running processes, such as when using the `ps` command\.

**Using Self\-Signed Certificates**

Self\-signed certificates are not recommended as certificates\. Self\-signed certificates may not be revoked, and self\-signed certificates may not conform to internal security requirements\.