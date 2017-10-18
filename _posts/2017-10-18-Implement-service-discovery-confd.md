---
layout: post
title:  "Implement service discovery - confd"
categories: Micro_Services
tags:  confd etcd nginx
author: Bo Chen
---

* content
{:toc}

### get the nginx image

	docker pull nginx:stable

### start a nginx container as the load balancer

    docker run -d --name nginx -p 80:80 nginx:stable

### update the nginx config to add the proxy_pass config

	#/etc/nginx/conf.d/default.conf

    location / {
        #root   /usr/share/nginx/html;
        #index  index.html index.htm;
        proxy_pass http://myapp1;
    }


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
	dest = "/etc/nginx/conf.d/upstream.conf"
	keys = [
		"/resources/hello-world/",
	]

### Create the source template

Source templates are Golang text templates.

	/etc/confd/templates/myconfig.conf.tmpl

upstream myapp1 {
        {\{range getvs "/resources/hello-world/*"}\}
        server ralbz001071.cloud.dst.ibm.com{\{.}\};
        {\{end}\}
}
	NOTE: remove the "\" between the "{\{" or "}\}"

### Setup the etcd with proper value

	docker run -P tutum/hello-world

### Process the template

confd supports two modes of operation daemon and onetime. In daemon mode confd polls a backend for changes and updates destination configuration files if necessary.

	confd -onetime -backend etcd -node http://9.37.1.71:2379/
	
Output:

    root@25d7ad635646:/etc/confd/conf.d# /opt/confd/bin/confd -onetime -backend etcd -node http://9.37.1.71:2379/
    2017-10-18T15:18:14Z 25d7ad635646 /opt/confd/bin/confd[4639]: INFO Backend set to etcd
    2017-10-18T15:18:14Z 25d7ad635646 /opt/confd/bin/confd[4639]: INFO Starting confd
    2017-10-18T15:18:14Z 25d7ad635646 /opt/confd/bin/confd[4639]: INFO Backend source(s) set to http://9.37.1.71:2379/

cat /etc/nginx/conf.d/upstream.conf
	
Output:

    root@25d7ad635646:/etc/confd/conf.d# cat /etc/nginx/conf.d/upstream.conf
    upstream myapp1 {

            server ralbz001071.cloud.dst.ibm.com:32774;

            server ralbz001071.cloud.dst.ibm.com:32775;

            server ralbz001071.cloud.dst.ibm.com:32776;

    }

### Reload the nginx configuration

    nginx -s reload

ref <https://github.com/kelseyhightower/confd/blob/master/docs/quick-start-guide.md>