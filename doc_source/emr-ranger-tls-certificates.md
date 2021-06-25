# Store TLS certificates in AWS Secrets Manager<a name="emr-ranger-tls-certificates"></a>

The Ranger plugins installed on an Amazon EMR cluster and the Ranger Admin server must communicate over TLS to ensure that policy data and other information sent cannot be read if they are intercepted\. EMR also mandates that the plugins authenticate to the Ranger Admin server by providing its own TLS certificate and perform two\-way TLS authentication\. This setup required four certificates to be created: two pairs of private and public TLS certificates\. For instructions on installing the certificate to your Ranger Admin server, see [Set up Ranger Admin server](emr-ranger-admin.md)\. To complete the setup, the Ranger plugins installed on the EMR cluster need two certificates: the public TLS certificate of your admin server, and the private certificate that the plugin will use to authenticate against the Ranger Admin server\. To provide these TLS certificates, they must be in the AWS Secrets Manager and provided in a EMR Security Configuration\.

**Note**  
It is strongly recommended, but not required, to create a certificate pair for each of your applications to limit impact if one of the plugin certificates becomes compromised\.

**Note**  
You need to track and rotate certificates prior to their expiration date\. 

## Certificate format<a name="emr-ranger-tls-cert-format"></a>

Importing the certificates to the AWS Secrets Manager is the same regardless of whether it is the private plugin certificate or the public Ranger admin certificate\. Before importing the TLS certificates, the certificates must be in 509x PEM format\.

An example of a public certificate is in the format:

```
-----BEGIN CERTIFICATE-----
...Certifcate Body...
-----END CERTIFICATE-----
```

An example of a private certificate is in the format:

```
-----BEGIN PRIVATE KEY-----
...Private Certifcate Body...
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
...Trust Certificate Body...
-----END CERTIFICATE-----
```

The private certificate should also contain a trust certificate as well\.

You can validate that the certificates are in the correct format by running the following command:

```
openssl x509 -in <PEM FILE> -text
```

## Importing a certificate to the AWS Secrets Manager<a name="emr-ranger-tls-cert-import"></a>

When creating your Secret in the Secrets Manager, choose **Other type of secrets** under **secret type** and paste your PEM encoded certificate in the **Plaintext** field as shown below\.

![\[Importing a certificate to AWS Secrets Manager.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/ranger-tls-cert-import.png)