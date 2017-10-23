---
layout: post
title:  "Configure Center and Service Registration and Discovery - Part 1"
categories: Micro_Services
tags:  etcd discovery registrator 注册发现
author: Bo Chen
---
# Configure Center and Service Registration and Discovery - Part 1

## Setup etcd

```bash
docker run -p 2379:2379 -p 2380:2380 --volume=/micros/micros/volume/etcd/node1:/etcd-data --name etcd quay.io/coreos/etcd:latest /usr/local/bin/etcd --data-dir=/etcd-data --name node1 --initial-advertise-peer-urls http://9.37.1.71:2380 --listen-peer-urls http://0.0.0.0:2380 --advertise-client-urls http://9.37.1.71:2379 --listen-client-urls http://0.0.0.0:2379 --initial-cluster node1=http://9.37.1.71:2380
```

curl -X PUT http://9.37.1.71:2379/v2/keys/message -d value="Hello1"
curl http://9.37.1.71:2379/v2/keys/message

curl -X PUT http://9.51.180.179:2379/v2/keys/message -d value="Hello1"

curl http://ralbz001071.cloud.dst.ibm.com:2379/v2/keys/message

## Setup registrator

* etcd

```bash
docker run -d --name=registrator --net=host --volume=/var/run/docker.sock:/tmp/docker.sock gliderlabs/registrator:latest etcd://9.37.1.71:2379/resources
```

* consul

```bash
$ docker run -d \
    --name=registrator \
    --net=host \
    --volume=/var/run/docker.sock:/tmp/docker.sock \
    gliderlabs/registrator:latest \
      consul://localhost:8500
```

## Setup confd

### Install confd

```bash
wget https://github.com/kelseyhightower/confd/releases/download/v0.14.0/confd-0.14.0-linux-amd64
mkdir -p /opt/confd/bin
mv confd-0.14.0-linux-amd64 /opt/confd/bin/confd
chmod +x /opt/confd/bin/confd
export PATH="$PATH:/opt/confd/bin"
```

### Create the confdir

`sudo mkdir -p /etc/confd/{conf.d,templates}`

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
	2014-07-08T20:38:36-07:00 confd[16252]: INFO Target config /tmp/myconfig.conf has been updated
The dest configuration file should now be in sync.

	cat /tmp/myconfig.conf  
	
Output:

	# This a comment
	[myconfig]
	database_url = db.example.com
	database_user = rob


curl http://9.37.1.71:2379/v2/keys/myapp/database/url
curl -X PUT http://${HostIP}:2379/v2/keys/message -d value="Hello"




## Setup nginx

### get the nginx image

`docker pull nginx:stable`

## File list

startup

```bash
#!/bin/bash
confd -onetime -sync-only -backend etcd -node http://9.37.1.71:2379/
sleep 5
confd -interval 10 -backend etcd -node http://9.37.1.71:2379/ &
nginx -g "daemon off;"
```

default.conf

```config
    location / {
        #root   /usr/share/nginx/html;
        #index  index.html index.htm;
        proxy_pass http://myapp1;
    }
```

myconfig.conf.tmpl

```config
upstream myapp1 {
        {{range getvs "/resources/reading-env-info/*"}}
        server ralbz001071.cloud.dst.ibm.com{{.}};
        {{end}}
}
```

myconfig.toml

```config
[template]
src = "myconfig.conf.tmpl"
dest = "/etc/nginx/conf.d/upstream.conf"
keys = [
	"/resources/reading-env-info/",
]
reload_cmd = "nginx -s reload"
```

Dockerfile

```docker
FROM nginx:stable
# ADD https://github.com/kelseyhightower/confd/releases/download/v0.14.0/confd-0.14.0-linux-amd64 /usr/local/bin/confd
ADD confd-0.14.0-linux-amd64 /usr/local/bin/confd
ADD startup /usr/local/bin/startup
RUN chmod +x /usr/local/bin/startup && chmod +x /usr/local/bin/confd && mkdir -p /etc/confd/{conf.d,templates}
ADD myconfig.toml /etc/confd/conf.d/myconfig.toml
ADD myconfig.conf.tmpl /etc/confd/templates/myconfig.conf.tmpl
ADD default.conf /etc/nginx/conf.d
CMD ["/bin/bash", "startup"]
```