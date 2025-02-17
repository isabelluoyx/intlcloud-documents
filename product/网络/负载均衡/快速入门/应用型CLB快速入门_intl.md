The public network application-based cloud load balancer launched by Tencent Cloud allows users to configure the domain name/URL-based forwarding rules to forward requests to different real servers. Also, the redirection feature of public network application-based cloud load balancer support redirecting http requests as https requests, enabling some mobile http requests to automatically return https respond through the LoadBalance proxy. Here we show you the new features of public network application-based cloud load balancer via detailed configurations.

## 1. Creating CVMs and Building nginx Service.

### 1.1 Purchasing CVMs
On the [Purchasing CVMs](https://buy.cloud.tencent.com/cvm) page, select the appropriate models and images, set the initial password of the CVM, and then configure the security group (here for the convenience of test, you can open all the ports to Internet first and make restrictions in the future). In addition, make sure to activate public network traffic when purchasing CVM. Otherwise access may fail after LB is associated.

![](https://main.qcloudimg.com/raw/a41060bdb0fcfb2d12ae108ade7a2b07.png)

The CVM environment parameters used in this test are as follows (two CVMs was purchased):
>**CVM Information**
Region	Guangzhou
Availability zone	Guangzhou Zone 3
CVM billing method	Postpaid
Network billing method	Bill-by-traffic
Network	Basic network

>**Machine Configuration**
OS	CentOS 6.8 64-bit
CPU	1-core
Memory	2GB
System disk	20GB (cloud block storage)	
Data disk	380 GB (Premium cloud storage)
Public network bandwidth	1 Mbps

### 1.2 Building Environment
After purchasing, click the **Login** button on the CVM details page to directly log in to CVM, and then enter your user name and password to start building the nginx environment. Here we apply the easiest way to install nginx. If you need to install the latest version of nginx, go to the official website to download and decompress one for installation.
Install nginx:
```
yum -y install nginx  
```
Start nginx and an error occurs
```
service nginx start
```
Modify the configuration file
```
vim /etc/nginx/conf.d/default.conf

listen       80 default_server; 
listen       [::]:80 default_server;

Change the configuration to:
listen       80;   #Listening port 80
#listen       [::]:80 default_server;
```
Restart nginx
```
sudo service nginx restart
```
Now visit the public IP address of the CVM, you can see the page below:

![](https://mc.qcloudimg.com/static/img/7bd527bd16e7a2c7a6f93d0e00e897ea/002.png)

The default root directory of nginx is /usr/share/nginx/html, so you can directly modify or move the index.html static page under html to identify the particularity of this page.
```
vim /usr/share/nginx/html/index.html
```
Because application-based cloud load balancer can forward requests based on the path of RS, you can configure services under different paths to facilitate subsequent request distribution for cloud load balancer. We deploy the static pages under /image/ path of CVM1 and /text/ path of CVM2 respectively as follows:

Relevant commands are as follows:
```
cd /usr/share/nginx/html/
mkdir image/
cp -r index.html image/    # For the other CVM, you can deploy the page to the text path
```

### 1.3 Authentication Service
Now, if the deployed page displays when you access the CVM's public network IP+ path, it indicates that the deployment in the first step is successful.
CVM1 /image page

![](https://mc.qcloudimg.com/static/img/2e10d11bb030dc2627ade5656690a483/003.png)

CVM2 /text page

![](https://mc.qcloudimg.com/static/img/9f063edb7307936199eb44ba72958e8a/004.png)

## 2. Purchasing and Configuring Public Network Application-based LB

### 2.1 Purchasing Application-based Cloud Load Balancer
Select application-based cloud load balancer on the [Purchasing CLBs](https://buy.cloud.tencent.com/lb) page. Please note that after you select the cloud load balancer in a region (for example, LB in Guangzhou), you can only bind intra-region backend CVMs of different availability zones (for example, CVMs in Guangzhou Zone 2 and Guangzhou Zone 3). After the application-based LB is created, you can explore the rich features of it.

![](https://main.qcloudimg.com/raw/f6d73eed167081e5c051cd2eaba22750.png)

### 2.2 Configuring Listeners, Forwarding Groups and Forwarding Rules, and Binding CVMs
After purchasing, you can view the information of listener bound to the LB instance on the **LB Details** -> **Listener Management** page. Click **New** to create an HTTP listener.

![](https://main.qcloudimg.com/raw/432c3ca99d1c68ea443d1f02d6fc5201.png)

Enter the listener name and listening port (here is port 80 by default) when creating the Layer-7 HTTP listener. After the listener is created, click **Create Forwarding Rule** to configure a domain name and URL for the listener. Wildcard and regularization are supported, but there are some restrictions. For more information, please see [Configuration Description](https://cloud.tencent.com/document/product/214/6744). For load balancing mode, you can select polling by weight. If you do not want the connection to fall on the same backend CVM, you can set the session persistence to be disabled by default in the step 3 of configuration.

![](https://main.qcloudimg.com/raw/d359773515c7136cf0512b43bfbf13ce.png)

After the forwarding rule is created, you can see that forwarding groups and forwarding rules have been configured for www.example.com/image/ under the listener. Then you can click **Bind CVM** to select the CVM for which we have just configured the service. When binding CVM, the backend port 80 is set as the default listening port. The configuration of application-based cloud load balancer is flexible, so you can bind CVMs of different backend ports under the same listener.

![](https://main.qcloudimg.com/raw/1104d272ec46b360f76d91bdac1f5a13.png)

Next, we can continue to create an HTTPS listener, which requires a server certificate at least for one-way authentication. Here you can upload your own certificates, or select the existing certificates, or apply for a certificate on the SSL certificate platform. We set port 443 for HTTPS protocol by default.

The subsequent listener configuration procedures are similar. After the configuration is completed, we can view the architecture under the LB:

![](https://main.qcloudimg.com/raw/f8fe8527b9bd60ee784dd4965bd7e53f.png)


### 2.2 Authentication Service
After finishing the configuration, we can verify whether the architecture takes effect. First, we need to perform the hosts operation for domain names of the two listeners. That is, modify the hosts file in C:\Windows\System32\drivers\etc to map the VIP of LB instance to the two domains.

![](https://mc.qcloudimg.com/static/img/dbdf71ada5ab53a98150a1677e700770/012.png)

To verify whether the hosts operation is successful or not, you can type "cmd" into the search bar on the local machine, and then use the ping command to check whether the domain name is successfully bound to the VIP. If there is a data packet, it indicates that the binding is successful.

![](https://main.qcloudimg.com/raw/7ba641fd3adf0851eb46d464e89e4e2a.png)

Next, you can enter `http://www.example.com/image/` and `https://www.example2.com/text/` to test whether a request can access RS via LB. (Note: the "/" of image/ and text/ is required, because it indicates image and text are two default directories instead of files with names of "image" and "text".)

![](https://mc.qcloudimg.com/static/img/82360f96ea78984030d7378b35ee48e0/014.png)

![](https://mc.qcloudimg.com/static/img/bd9bc8b9d925c8cb54740d448eead43c/015.png)

The results shown in the above figure indicate that we can access different backend CVMs via different ***domain names + URLs*** under a LB instance, that is, ***"content-based routing"*** feature is realized. Then, the redirection feature can work in the two scenarios below:

(1) Forced https: When Web service is accessed by PC and mobile browsers via http requests, you want https respond to be returned through LoadBalance proxy. By default, https is used by browsers to access web pages.

(2) Custom redirection: When Web services need to be temporarily deactivated (in case of selling-out of e-commerce, page maintenance, and upgrade), redirect capability is necessary. Without redirections, visitors can only get a 404/503 error message due to the old address in users' favorites and search engine database, which reduces user experience and results in access traffic loss. In addition, the search engine scores accumulated on this page are also wasted.

Next, we can experience this feature through actual operations, that is, redirect the requests in the configured HTTP listener to the HTTPS listener.

## 3. Configuring Redirection

The redirection configuration is divided into manual redirection and automatic redirection. Automatic redirection is mainly designed for such situation: there are many paths under the domain name, and the system needs to automatically create an HTTP listener for the existing HTTPS: 443 listener for forwarding. After the HTTP listener is created successfully, the HTTP: 80 address can be automatically redirected as HTTPS address: 443 for access. We use manual redirection configuration in this document. For more information, please see [Redirection Configuration](https://intl.cloud.tencent.com/document/product/214/8839).

### 3.1 Manual Redirection Configuration
Select the Redirection Configuration tab on the LB details page, and create a new manual redirection configuration.

![](https://main.qcloudimg.com/raw/8df317764c2e109cbd020bdf6959fbaa.png)

Then select the original access protocol, port and domain name, and specify the destination protocol, port and domain name.

![](https://main.qcloudimg.com/raw/104c474d3cd60b1ed4c8d0c27679083d.png)

Click **Next** to select the original and redirected access paths. If there are many paths under the domain name, you can add multiple paths for redirection. Note: The path configuration does not allow loopback (that is, A-> B B-> C), and the configuration can only be performed in the same LB instance for now.

![](https://main.qcloudimg.com/raw/c5c24617b59f06fb3a58d42e7cc5bcfc.png)

After the redirection policy is configured, you can view the policy on the LB redirection configuration details page. In addition, you can find that in the original listener tree diagram, a redirection identifier is added to the path of the HTTP listener to indicate that the bound real servers in this path will no longer receive requests because requests will be redirected to the HTTPS listener you just configured.

![](https://main.qcloudimg.com/raw/3633dbcb3466b6e072542ac575e2d929.png)


### 3.2 Authentication Service
Finally, we can access `http://www.example.com/image/` to verify whether the request is automatically redirected to the address `https://www.example2.com/text/`.
If the following page appears after you enter `http://www.example.com/image/`, the redirection is configured successfully.

![](https://mc.qcloudimg.com/static/img/591798ef620f8a72d9904197ca06c9a2/020.png)

