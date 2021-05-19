# Option 2, Part 2: Configure Proxy Settings to View Websites Hosted on the Master Node<a name="emr-connect-master-node-proxy"></a>

If you use an SSH tunnel with dynamic port forwarding, you must use a SOCKS proxy management add\-on to control the proxy settings in your browser\. Using a SOCKS proxy management tool allows you to automatically filter URLs based on text patterns and to limit the proxy settings to domains that match the form of the master node's public DNS name\. The browser add\-on automatically handles turning the proxy on and off when you switch between viewing websites hosted on the master node and those on the Internet\. To manage your proxy settings, configure your browser to use an add\-on such as FoxyProxy or SwitchyOmega\. 

For more information about creating an SSH tunnel, see [Option 2, Part 1: Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding](emr-ssh-tunnel.md)\. For more information about the available web interfaces, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\. 



Include the following settings when you set up your proxy add\-on:
+ Use **localhost** as the host address\.
+ Use the same local port number that you selected to establish the SSH tunnel with the master node in [Option 2, Part 1: Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding](emr-ssh-tunnel.md)\. For example, port *8157*\. This port must also match the port number you use in PuTTY or any other terminal emulator you use to connect\.
+ Specify the **SOCKS v5** protocol\. SOCKS v5 lets you optionally set up user authorization\.
+ **URL Patterns**

  The following URL patterns should be allow\-listed and specified with a wildcard pattern type:
  + The **\*ec2\*\.amazonaws\.com\*** and **\*10\*\.amazonaws\.com\*** patterns to match the public DNS name of clusters in US regions\.
  + The **\*ec2\*\.compute\*** and **\*10\*\.compute\*** patterns to match the public DNS name of clusters in all other regions\.
  +  A **10\.\*** pattern to provide access to the JobTracker log files in Hadoop\. Alter this filter if it conflicts with your network access plan\.
  + The **\*\.ec2\.internal\*** and **\*\.compute\.internal\*** patterns to match the private \(internal\) DNS names of clusters in the `us-east-1` region and all other regions, respectively\.

## Example: Configure FoxyProxy for Firefox<a name="emr-connect-foxy-proxy-chrome"></a>

The following example demonstrates a FoxyProxy Standard \(version 7\.5\.1\) configuration for Mozilla Firefox\.

FoxyProxy provides a set of proxy management tools\. It lets you use a proxy server for URLs that match patterns corresponding to domains used by the Amazon EC2 instances in your Amazon EMR cluster\.<a name="foxy-proxy"></a>

**To install and configure FoxyProxy using Mozilla Firefox**

1. In Firefox, go to [https://addons\.mozilla\.org/](https://addons.mozilla.org/), search for FoxyProxy Standard, and follow the instructions to add FoxyProxy to Firefox\.

1. Using a text editor, create a JSON file named `foxyproxy-settings.json` from the following example configuration\.

   ```
   {
     "k20d21508277536715": {
       "active": true,
       "address": "localhost",
       "port": 8157,
       "username": "",
       "password": "",
       "type": 3,
       "proxyDNS": true,
       "title": "emr-socks-proxy",
       "color": "#0055E5",
       "index": 9007199254740991,
       "whitePatterns": [
         {
           "title": "*ec2*.amazonaws.com*",
           "active": true,
           "pattern": "*ec2*.amazonaws.com*",
           "importedPattern": "*ec2*.amazonaws.com*",
           "type": 1,
           "protocols": 1
         },
         {
           "title": "*ec2*.compute*",
           "active": true,
           "pattern": "*ec2*.compute*",
           "importedPattern": "*ec2*.compute*",
           "type": 1,
           "protocols": 1
         },
         {
           "title": "10.*",
           "active": true,
           "pattern": "10.*",
           "importedPattern": "http://10.*",
           "type": 1,
           "protocols": 2
         },
         {
           "title": "*10*.amazonaws.com*",
           "active": true,
           "pattern": "*10*.amazonaws.com*",
           "importedPattern": "*10*.amazonaws.com*",
           "type": 1,
           "protocols": 1
         },
         {
           "title": "*10*.compute*",
           "active": true,
           "pattern": "*10*.compute*",
           "importedPattern": "*10*.compute*",
           "type": 1,
           "protocols": 1
         },
         {
           "title": "*.compute.internal*",
           "active": true,
           "pattern": "*.compute.internal*",
           "importedPattern": "*.compute.internal*",
           "type": 1,
           "protocols": 1
         },
         {
           "title": "*.ec2.internal* ",
           "active": true,
           "pattern": "*.ec2.internal*",
           "importedPattern": "*.ec2.internal*",
           "type": 1,
           "protocols": 1
         }
       ],
       "blackPatterns": []
     },
     "logging": {
       "size": 100,
       "active": false
     },
     "mode": "patterns",
     "browserVersion": "68.12.0",
     "foxyProxyVersion": "7.5.1",
     "foxyProxyEdition": "standard"
   }
   ```

1. Open the Firefox **Manage Your Extensions** page \(go to **about:addons**, then choose **Extensions**\)\.

1. Choose **FoxyProxy Standard**, then choose the more options button \(the button that looks like an ellipsis\)\.

1. Select **Options** from the dropdown\.

1. Choose **Import Settings** from the left menu\.

1. On the **Import Settings** page, choose **Import Settings** under **Import Settings from FoxyProxy 6\.0\+**, browse to the location of the `foxyproxy-settings.json` file you created, select the file, and choose **Open**\. 

1. Choose **OK** when prompted to overwrite the existing settings and save your new configuration\.

## Example: Configure SwitchyOmega for Chrome<a name="switchyomega"></a>

The following example demonstrates how to set up the SwitchyOmega extension for Google Chrome\. SwitchyOmega lets you configure, manage, and switch between multiple proxies\.

**To install and configure SwitchyOmega using Google Chrome**

1. Go to [https://chrome\.google\.com/webstore/category/extensions](https://chrome.google.com/webstore/category/extensions), search for **Proxy SwitchyOmega**, and add it to Chrome\.

1. Choose **New profile** and enter `emr-socks-proxy` as the profile name\.

1. Choose **PAC profile** and then **Create**\. [Proxy Auto\-Configuration \(PAC\)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling/Proxy_Auto-Configuration_(PAC)_file) files help you define an allow list for browser requests that should be forwarded to a web proxy server\.

1. In the **PAC Script** field, replace the contents with the following script that defines which URLs should be forwarded through your web proxy server\. If you specified a different port number when you set up your SSH tunnel, replace *8157* with your port number\.

   ```
   function FindProxyForURL(url, host) {
       if (shExpMatch(url, "*ec2*.amazonaws.com*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "*ec2*.compute*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "http://10.*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "*10*.compute*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "*10*.amazonaws.com*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "*.compute.internal*")) return 'SOCKS5 localhost:8157';
       if (shExpMatch(url, "*ec2.internal*")) return 'SOCKS5 localhost:8157';
       return 'DIRECT';
   }
   ```

1. Under **Actions**, chose **Apply changes** to save your proxy settings\.

1. On the Chrome toolbar, choose SwitchyOmega and select the `emr-socks-proxy` profile\.

## Access a Web Interface in the Browser<a name="connect-to-web-ui-browser"></a>

To open a web interface, enter the public DNS name of your master or core node followed by the port number for your chosen interface into your browser address bar\. The following example shows the URL you would enter to connect to the Spark HistoryServer\.

```
http://master-public-dns-name:18080/				
```

For instructions on retrieving the public DNS name of a node, see [Retrieve the Public DNS Name of the Master Node](emr-connect-master-node-ssh.md#emr-connect-master-dns)\. For a complete list of web interface URLs, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\.