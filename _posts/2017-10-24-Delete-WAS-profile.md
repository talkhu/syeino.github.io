---
layout: post
title:  "Delete WAS profile"
categories: Operation
tags:  WAS
excerpt: null
mathjax: true
author: Internet
---

* content
{:toc}

    1
	操作前需要确认执行操作的用户具有WAS管理员权限，在aix及linux平台root用户即可，windows平台则正常系统用户即可。本步骤中使用windows平台测试，使用的是系统管理官员
	2
	
	停WAS服务  
	
	在删除前一定要确认将WAS服务停止，在windows平台可以在services.msc中查找确认启动状态，在linux及aix平台可以使用ps -ef|grep was来确认
	3
	
	确认profile名称
	
	cd D:\Program Files (x86)\IBM\WebSphere\AppServer\bin>
	
	manageprofiles.bat -listProfiles
	4
	
	删除profile定义
	
	cd D:\Program Files (x86)\IBM\WebSphere\AppServer\bin>
	
	manageprofiles.bat  -delete -profileName myprofile
	5
	
	删除概要文件所在目录
	
	到D:\Program Files (x86)\IBM\WebSphere\AppServer\删除profiles文件夹
