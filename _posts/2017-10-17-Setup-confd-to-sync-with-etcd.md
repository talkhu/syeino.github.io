---
layout: post
title:  "Setup confd to sync with etcd"
categories: Micro_Services
tags:  confd etcd
author: Bo Chen
---

* content
{:toc}

### get the nginx image

	docker pull nginx:stable

### Install confd  

	wget https://github.com/kelseyhightower/confd/releases/download/v0.14.0/confd-0.14.0-linux-amd64
	mkdir -p /opt/confd/bin
	mv confd-0.14.0-linux-amd64 /opt/confd/bin/confd
	chmod +x /opt/confd/bin/confd
	export PATH="$PATH:/opt/confd/bin"  

### Create the confdir  

	sudo mkdir -p /etc/confd/{conf.d,templates}  
	
### Create a template resource config

Template resources are defined in TOML config files under the confdir.

	/etc/confd/conf.d/myconfig.toml

	[template]
	src = "myconfig.conf.tmpl"
	dest = "/tmp/myconfig.conf"
	keys = [
		"/myapp/database/url",
		"/myapp/database/user",
	]

### Create the source template

Source templates are Golang text templates.

	/etc/confd/templates/myconfig.conf.tmpl

	[myconfig]
	database_url = {{getv "/myapp/database/url"}}
	database_user = {{getv "/myapp/database/user"}}

### Setup the etcd with proper value

	curl -X PUT http://9.37.1.71:2379/v2/keys/myapp/database/url -d value="db.example.com"
	curl -X PUT http://9.37.1.71:2379/v2/keys/myapp/database/user -d value="rob"

### Process the template

confd supports two modes of operation daemon and onetime. In daemon mode confd polls a backend for changes and updates destination configuration files if necessary.

	confd -onetime -backend etcd -node http://9.37.1.71:2379/
	
Output:

	2014-07-08T20:38:36-07:00 confd[16252]: INFO Target config /tmp/myconfig.conf out of sync
	2014-07-08T20:38:36-07:00 confd[16252]: INFO Target config /tmp/myconfig.conf has been updated The dest configuration file should now be in sync.

cat /tmp/myconfig.conf  
	
Output:

	# This a comment
	[myconfig]
	database_url = db.example.com
	database_user = rob

ref <https://github.com/kelseyhightower/confd/blob/master/docs/quick-start-guide.md>