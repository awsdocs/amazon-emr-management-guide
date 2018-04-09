# Option 2, Part 2: Configure Proxy Settings to View Websites Hosted on the Master Node<a name="emr-connect-master-node-proxy"></a>

If you use an SSH tunnel with dynamic port forwarding, you must use a SOCKS proxy management add\-on to control the proxy settings in your browser\. Using a SOCKS proxy management tool allows you to automatically filter URLs based on text patterns and to limit the proxy settings to domains that match the form of the master node's public DNS name\. The browser add\-on automatically handles turning the proxy on and off when you switch between viewing websites hosted on the master node and those on the Internet\. To manage your proxy settings, configure your browser to use an add\-on such as FoxyProxy or SwitchySharp\. 

For more information about creating an SSH tunnel, see [Option 2, Part 1: Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding](emr-ssh-tunnel.md)\. For more information about the available web interfaces, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\. 

The following example demonstrates a FoxyProxy configuration using Google Chrome\. The relevant settings that are loaded from the configuration file in the example are as follows:
+ **Host or IP Address**—This is set to **localhost** with the Port set to **8157** in the example\. You should set this port to the local port number that you used to establish the SSH tunnel with the master node in [Option 2, Part 1: Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding](emr-ssh-tunnel.md)\. This port must also match the port number you use in PuTTY or other terminal emulator you use to connect\.
+ **SOCKS v5** configuration is specified\.
+ Login credentials are not specified\.
+ **URL Patterns**

  The following URL patterns are whitelisted and specified with a wildcard pattern type:
  + The **\*ec2\*\.amazonaws\.com\*** and **\*10\*\.amazonaws\.com\*** patterns match the public DNS name of clusters in US regions\.
  + The **\*ec2\*\.compute\*** and **\*10\*\.compute\*** patterns match the public DNS name of clusters in all other regions\.
  + The **10\.\*** pattern provides access to the JobTracker log files in Hadoop\. Alter this filter if it conflicts with your network access plan\.

## Configure FoxyProxy for Google Chrome<a name="emr-connect-foxy-proxy-chrome"></a>

You can configure FoxyProxy for Google Chrome, Mozilla Firefox, and Microsoft Internet Explorer\. FoxyProxy provides a set of proxy management tools that allow you to use a proxy server for URLs that match patterns corresponding to the domains used by the Amazon EC2 instances in your Amazon EMR cluster\.<a name="foxy-proxy-chrome"></a>

**To install and configure FoxyProxy using Google Chrome**

1. See [https://chrome\.google\.com/webstore/search/foxy%20proxy](https://chrome.google.com/webstore/search/foxy%20proxy) and follow the links and instructions to add FoxyProxy to Chrome\.

1. Using a text editor, create a file named `foxyproxy-settings.xml` with the following contents:

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <foxyproxy>
      <proxies>
         <proxy name="emr-socks-proxy" id="2322596116" notes="" fromSubscription="false" enabled="true" mode="manual" selectedTabIndex="2" lastresort="false" animatedIcons="true" includeInCycle="true" color="#0055E5" proxyDNS="true" noInternalIPs="false" autoconfMode="pac" clearCacheBeforeUse="false" disableCache="false" clearCookiesBeforeUse="false" rejectCookies="false">
            <matches>
               <match enabled="true" name="*ec2*.amazonaws.com*" pattern="*ec2*.amazonaws.com*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false" />
               <match enabled="true" name="*ec2*.compute*" pattern="*ec2*.compute*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false" />
               <match enabled="true" name="10.*" pattern="http://10.*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false" />
               <match enabled="true" name="*10*.amazonaws.com*" pattern="*10*.amazonaws.com*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false" />
               <match enabled="true" name="*10*.compute*" pattern="*10*.compute*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false" /> 
               <match enabled="true" name="*.compute.internal*" pattern="*.compute.internal*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false"/>
               <match enabled="true" name="*.ec2.internal* " pattern="*.ec2.internal*" isRegEx="false" isBlackList="false" isMultiLine="false" caseSensitive="false" fromSubscription="false"/>	  
   	   </matches>
            <manualconf host="localhost" port="8157" socksversion="5" isSocks="true" username="" password="" domain="" />
         </proxy>
      </proxies>
   </foxyproxy>
   ```

1. Manage extensions in Chrome \(go to **chrome://extensions**\)\.

1. Choose **Options** for FoxyProxy Standard\.

1. On the **FoxyProxy** page, choose **Import/Export**\.

1. On the **Import/Export** page, choose **Choose File**, browse to the location of the `foxyproxy-settings.xml` file you created, select the file, and choose **Open**\. 

1. Choose **Replace** when prompted to overwrite the existing settings\. 

1. For **Proxy mode**, choose **Use proxies based on their predefined patterns and priorities**\.

1. To open the web interfaces, in your browser's address bar, type *master\-public\-dns* followed by the port number or URL\. 

   For a complete list of web interfaces on the master node, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\.