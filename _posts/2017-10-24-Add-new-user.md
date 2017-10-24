---
layout: post
title:  "Add new user"
categories: Operation
tags:  Linux
excerpt: null
mathjax: true
author: Bo Chen
---

* content
{:toc}

	建立一个新的用户包括两个步骤，1)使用useradd命令完成一个新用户的初始化设置工作; 2)用passwd为这个新用户设置密码。
	
		useradd floatboat <回车>
		passwd floatboat <回车>  
	
	1、useradd所做的初始化操作已经包括在/home目录下为floatboat帐号建立一个名为floatboat的主目录。
	如果你不想使用这个缺省的目录，而希望把他的主目录放在/home/goal里，可以使用useradd的参数-d，命令如下：
	
	useradd -d /home/goal floatboat
	
	2、useradd的初始化操作还包括为用户单独建立一个与用户名同名的组(floatboat组)。这叫用户私有组的机制，与默认组机制相对应。
	对用户分组一是方便管理，二是可以明确权限。我们如果想让此用户加入一个已有的组的话，可以使用-g参数。
	例如我们想让floatboat加入webusers组，那么可以使用以下命令：
	
	useradd -g webusers floatboat
	
	同样的，我们还可以使用-G参数使他同时加入多个组，例如webusers和ftpusers：
	
	useradd -G ftpusers,webusers floatboat
	
	3、passwd命令为一个用户设置密码，但它实质上是一个修改密码的程序。
	
	4、你在增加一个新用户的时候，也可以设置用户登录的shell。缺省的，系统提供了/bin/bash。可以使用-s参数就可以指定。例如
	
	useradd -d /www -s /usr/bin/passwd floatboat
	
	这些参数是可以一块使用的，如上例所示，它表示增加新用户，并把其主目录路径设置在/www，登录的shell为/usr/bin/passwd。
		
	5、删除一个用户可以使用userdel命令，直接带用户名做参数就可以了。