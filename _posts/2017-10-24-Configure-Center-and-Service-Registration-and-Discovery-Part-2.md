---
layout: post
title:  "Configure Center and Service Registration and Discovery - Part 2"
categories: Micro_Services
tags:  etcd discovery registrator 注册发现
author: Bo Chen
---

* content
{:toc}

# Prerequisite

* Should have an etcd server `$etcd_host` or cluster available.
* Should have a host `$service_host` for backend service and registrator should running on it.
* Should have a host `$proxy_host` for nginx as reverse proxy.

# Host information

* The following 3 host can be the same, like `localhost`

```bash
    $etcd_host =  x.x.x.x:2379
    $service_host = xxx.com
    $proxy_host = yyy.com
```

# Steps to demostrate service discovery and registrator

* Build the images for nginx and the env-info project

```bash
    cd project/nginx-discovery
    sudo docker build . -t nginx-discovery
    cd reading-env-info
    sudo docker build . -t reading-env-info (# NOTE: Only if jenkins item not work)
```

* Run the nginx container for service discovery
    `sudo docker run -d -p 80:80 --name nginx-micros nginx-discovery`

* Prepare environment and ensure at least 1 backend service running.

```bash
    ssh $service_host
    sudo docker ps -a | grep reading-env-info
    sudo docker rm -f container-id #optional: ensure keep at least 1 container alive
    sudo docker run -dP reading-env-info:Build2 #optional
    sudo docker ps -a | grep reading-env-info #note down the port
```

* Display the current webpage and note down the current host info and etcd value.

  * Open <http://$service_host:port> and note down the host info and etcd info
  * Open <http://$proxy_host/> to ensure it is exactly the same with $service_host

* Refresh the webpage to ensure no change.

  * Reload <http://$proxy_host/> several times to ensure no change.

* Change the etcd value to show the configuration center.

    `curl -X PUT http://$etcd_host/v2/keys/reading-env-info/message -d value="new value"`

* Open the URL to verify the result.

  * Open <http://$proxy_host/> to ensure the 2nd line(`Value from etcd`) updated to new value.

* Add the containers to show the service registration.

  * On $service_host, run following cmd several times for more containers

    `sudo docker run -dP reading-env-info:Build2`

  * Get the port# of the backend services

    `sudo docker ps -a | grep reading-env-info(note down the port)`

  * Open the URL to verify the result

    * open <http://$service_host:port> and note down the host info
    * open <http://$proxy_host/> and reload several times to ensure it is exactly the same with $service_host

* Reduce the containers to show the un-registration.

  * On $service_host, run the following cmd to get the container ID.
    ```sudo docker ps -a | grep reading-env-info(note down the container ID/name)```
  * Repeat following cmd several times and ensure at least 1 container left.
    ```sudo docker rm -f container_ID/name```
  * Check the port# of the backend services
    ```sudo docker ps -a | grep reading-env-info(note down the port)```
  * Verify the result.
    * Open <http://$service_host:port> and note down the host info
    * Open <http://$proxy_host/> and reload several times to ensure it is exactly the same with $service_host

**NOTE: The interval of refresh is setting as 10 seconds.**