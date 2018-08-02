---
layout: post_layout
title: 小程序开发时的问题总结
time: On Thursday, Aug 2, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

#### 小程序开发时的问题总结

* 后台准备工作：
	* 1、需要遵循https协议（文章最后给出阿里云的文档）
	* 2、不能使用ip请求接口，需要域名（不再赘述）


* 开发中技术难点/耗时问题：
	* 1、微信登录（小程序/服务号）
		* 小程序登录时，获取openid相对比较简单，由前端传个js_code，调如下接口即可：https://api.weixin.qq.com/sns/jscode2session?appid="+appid+"&secret="+secret+"&js_code="+js_code+"&grant_type=authorization_code 

		* 另：服务号（非小程序），获取用户信息时，相对比较麻烦。
	微信获取用户信息文档：https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140842
			* 1、第一步获取code时比较麻烦，请求完接口后，返回到回调路径上，不能直接用ajax来请求获取（在这里坑了好多次，我的理解，ajax要拿到的数据是本次接口返回的数据，而微信给的这个接口有回调，到另一个页面上了，所以不能用ajax。）用链接请求，加上自己的当前路径url
			* 2、后台处理后返回到自己的页面上
			* 3、在页面上存上前端请求过来的路径url和微信返回的code，重定向到前端的一个空白页
			* 4、前端空白页的处理：把code存里来，返回到接收到的路径url
			* 后面步骤按照微信的接口即可
	* 2、模板消息发送
		* 微信模板消息接口文档：https://developers.weixin.qq.com/miniprogram/dev/api/notice.html
		* 易错点：
			* 1、获取accessToken时，用的不是登录时的accessToken的请求
			* 2、前端获取formId https://developers.weixin.qq.com/miniprogram/dev/component/form.html
			* 3、注意data里的数据格式，data下的keyword1也是个类，属性是value

	* 3、分享时，后台获取签名
		* 易错点：
			* 1、获取ticket时，注意后面的type参数，给jsapi值。还有个卡卷支付获取，也是同样的接口，只是type值不一样
			* 2、注意noncestr属性，后台给时是全小写，前端要用nonceStr属性来接收（这应该是微信团队的问题）
			* 3、文档里有提到，accessToken、ticket要放缓存里，不然请求太频繁会有问题



阿里云技术支持

* nginx配置https

	* 文件说明：

	* 1. 证书文件214520393XXXX.pem，包含两段内容，请不要删除任何一段内容。

	* 2. 如果是证书系统创建的CSR，还包含：证书私钥文件214520393250244.key。

		* ( 1 ) 在Nginx的安装目录下创建cert目录，并且将下载的全部文件拷贝到cert目录中。如果申请证书时是自己创建的CSR文件，请将对应的私钥文件放到cert目录下并且命名为214520393250244.key；

		* ( 2 ) 打开 Nginx 安装目录下 conf 目录中的 nginx.conf 文件，找到：

		```java
		
		 HTTPS server
		 server {
		 listen 443;
		 server_name localhost;
		 ssl on;
		 ssl_certificate cert.pem;
		 ssl_certificate_key cert.key;
		 ssl_session_timeout 5m;
		 ssl_protocols SSLv2 SSLv3 TLSv1;
		 ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
		 ssl_prefer_server_ciphers on;
		 location / {
		
		
		}
		}
		
		```

		* ( 3 ) 将其修改为 (以下属性中ssl开头的属性与证书配置有直接关系，其它属性请结合自己的实际情况复制或调整) :
		```java
		
		server {
		    listen 443;
		    server_name localhost;
		    ssl on;
		    root html;
		    index index.html index.htm;
		    ssl_certificate   cert/214520393250244.pem;
		    ssl_certificate_key  cert/214520393250244.key;
		    ssl_session_timeout 5m;
		    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
		    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
		    ssl_prefer_server_ciphers on;
		    location / {
		        root html;
		        index index.html index.htm;
		    }
		}
		保存退出。
		```

		* ( 4 )重启 Nginx。


* linux tomcat配置https

	* Tomcat支持JKS格式证书，从Tomcat7开始也支持PFX格式证书，两种证书格式任选其一。
	
	* 文件说明：

	* 1. 证书文件214520393250244.pem，包含两段内容，请不要删除任何一段内容。

	* 2. 如果是证书系统创建的CSR，还包含：证书私钥文件214520393250244.key、PFX格式证书文件214520393250244.pfx、PFX格式证书密码文件pfx-password.txt。

		* 1、证书格式转换

			* 在Tomcat的安装目录下创建cert目录，并且将下载的全部文件拷贝到cert目录中。如果申请证书时是自己创建的CSR文件，附件中只包含214520393250244.pem文件，还需要将私钥文件拷贝到cert目录，命名为214520393250244.key；如果是系统创建的CSR，请直接到第2步。
			* 到cert目录下执行如下命令完成PFX格式转换命令，此处要设置PFX证书密码，请牢记：openssl pkcs12 -export -out 214520393250244.pfx -inkey 214520393250244.key -in 214520393250244.pem
		* 2、PFX证书安装

			* 找到安装Tomcat目录下该文件server.xml,一般默认路径都是在 conf 文件夹中。找到 &lt;Connection port="8443"标签，增加如下属性：keystoreFile="cert/214520393250244.pfx" keystoreType="PKCS12" 此处的证书密码，请参考附件中的密码文件或在第1步中设置的密码 keystorePass="证书密码" 完整的配置如下，其中port属性根据实际情况修改：

```java
<Connector port="8443"
    protocol="HTTP/1.1"
    SSLEnabled="true"
    scheme="https"
    secure="true"
    keystoreFile="cert/214520393250244.pfx"
    keystoreType="PKCS12"
    keystorePass="证书密码"
    clientAuth="false"
    SSLProtocol="TLSv1+TLSv1.1+TLSv1.2"
    ciphers="TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_256_CBC_SHA256"/>
    
 ```
 
		* 3、JKS证书安装(帮助)

			* ( 1 ) 使用java jdk将PFX格式证书转换为JKS格式证书(windows环境注意在%JAVA_HOME%/jdk/bin目录下执行)

keytool -importkeystore -srckeystore 214520393250244.pfx -destkeystore your-name.jks -srcstoretype PKCS12 -deststoretype JKS
回车后输入JKS证书密码和PFX证书密码，强烈推荐将JKS密码与PFX证书密码相同，否则可能会导致Tomcat启动失败。
			* ( 2 ) 找到安装 Tomcat 目录下该文件Server.xml，一般默认路径都是在 conf 文件夹中。找到 &lt;Connection port="8443"标签，增加如下属性：

keystoreFile="cert/your-name.jks"
keystorePass="证书密码"
完整的配置如下，其中port属性根据实际情况修改：

```java
<Connector port="8443"
    protocol="HTTP/1.1"
    SSLEnabled="true"
    scheme="https"
    secure="true"
    keystoreFile="cert/your-name.jks"
    keystorePass="证书密码"
    clientAuth="false"
    SSLProtocol="TLSv1+TLSv1.1+TLSv1.2"
    ciphers="TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_256_CBC_SHA256"/>
    
```

( 注意:不要直接拷贝所有配置，只需添加 keystoreFile,keystorePass等参数即可，其它参数请根据自己的实际情况修改 )

		* 4、 重启 Tomcat。




