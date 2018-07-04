---
layout: post_layout
title: Mac OS下替代Xshell的FinalShell
time: On Wednesday, July 4, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

#### Mac OS下替代Xshell的FinalShell

* FinalShell For Mac的安装

	* Mac版，Linux版的安装，其实相差不大。这里主要是介绍下Mac的安装

	* 这个软件现在算是测试版，作者提示可能会有些小问题。不过我使用来看，基本上没遇到什么。安装也很方便，有一键安装脚本
	
	* [官网链接](http://www.hostbuf.com)

	* 点开Mac终端

	* 输入以下命令：

	``` java
	curl -o finalshell_install.sh http://www.hostbuf.com/downloads/finalshell_install.sh 回车
	
	chmod +x finalshell_install.sh 回车
	
	sudo ./finalshell_install.sh 回车
	```

	* 会自动进行安装，会要求你输入密码，按照提示输入即可

	* 安装完成后，在前往--应用程序下：
￼

* 三、运行FinalShell

	* 双击FinalShell运行，跳出对话框要求你输入密码，自动调用Java支持，等软件打开后，新增SSH连接：

	* 会弹出一个新窗口，要提醒大家的是，看看你的VPS上，SSH连接开的端口是多少。不是每个都是用的22端口，免的连接不上
￼
	* 一切配置好后，按确定就可以了。我这里面新建一个New名字的连接。在上面点右键，连接就可以

	* 连接成功后的界面如下，这软件本身也集成了一些FTP的功能，可以简单上传一些你的文件。

	![avatar](https://kujmsliwang.github.io/assets/img/finalShell.png){:height="80%" width="100%"}




