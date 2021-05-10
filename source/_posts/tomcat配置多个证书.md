---
title: 单个Tomcat配置多个域并配置多个证书
cover: 'https://static.ivan.fun/blog/httpstomcat.jpg'
index_img: 'https://static.ivan.fun/blog/httpstomcat.jpg'
tags:
  - Tomcat
categories: -Tomcat
abbrlink: eb59cee9
date: 2020-04-05 08:00:00
---
# 单个Tomcat配置多个域并配置多个证书 #
## 我的环境:tomcat8.5 阿里云https证书两个
### tomcat server.xml中配置如下
``` 
<Connector executor="tomcatThreadPool" port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
    				   connectionTimeout="20000"
    				   enableLookups="false"
    				   maxPostSize="10485760"
    				   URIEncoding="UTF-8"
    				   acceptCount="100"
    				   acceptorThreadCount="2"
    				   disableUploadTimeout="true"
    				   maxConnections="10000"
    				   SSLEnabled="true"
    				   defaultSSLHostConfigName="www.inteagle.com.cn"> <!--默认的域名-->
    <SSLHostConfig hostName="www.inteagle.com.cn">
    	<Certificate certificateKeystoreFile="/usr/local/src/apache-tomcat-8.5.23/cert/2315816_www.inteagle.com.cn.pfx" 
    		keystoreFile="/usr/local/src/apache-tomcat-8.5.23/cert/2315816_www.inteagle.com.cn.pfx" certificateKeystorePassword="yTMUtA2f" keystoreType="PKCS12" />
    </SSLHostConfig>
    <SSLHostConfig hostName="event-cameras.com">
    	<Certificate certificateKeystoreFile="/usr/local/src/apache-tomcat-8.5.23/cert/3699744_event-cameras.com.pfx" 
			keystoreFile="/usr/local/src/apache-tomcat-8.5.23/cert/3699744_event-cameras.com.pfx" certificateKeystorePassword="4s49rWb1"  keystoreType="PKCS12" />
    </SSLHostConfig>
</Connector>
```