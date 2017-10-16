---
layout: post
title:  "How to use docker to install etcd2"
categories: Micro_Services
tags:  docker etcd
author: Bo Chen
---

* content
{:toc}

### Run the docker container  

    docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
     --name etcd quay.io/coreos/etcd:v2.3.8 \
     /usr/local/bin/etcd \
     -name etcd0 \
     -advertise-client-urls http://${HostIP}:2379,http://${HostIP}:4001 \
     -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
     -initial-advertise-peer-urls http://${HostIP}:2380 \
     -listen-peer-urls http://0.0.0.0:2380 \
     -initial-cluster-token etcd-cluster-1 \
     -initial-cluster etcd0=http://${HostIP}:2380 \
     -initial-cluster-state new

### Put and get the key value from etcd  

     curl -X PUT http://${HostIP}:2379/v2/keys/message -d value="Hello"
     curl http://${HostIP}:2379/v2/keys/message

For more details, please refer <https://coreos.com/etcd/docs/latest/v2/docker_guide.html>
