---
layout: post
title:  "Configure Center and Service Registrator and Discovery"
categories: Micro_Services
tags:  etcd discovery registrator 注册发现
author: Bo Chen
---
# Configure Center and Service Registrator and Discovery

## Prerequisite

    1. Should have an etcd server($etcd_host) or cluster available.		
    2. Should have a host($service_host) for backend service and registrator should running on it.	
    3. Should have a host($proxy_host) for nginx as reverse proxy.

## Host information for micro service team

    $etcd_host =  Host_Micros:2379
    $service_host = Host_Micros
    $proxy_host = Host_Emma

    (NOTE: The above 3 host can be the same, like localhost)

## Steps to demostrate service discovery and registrator.

    1. Build the images for nginx and the env-info project  
        cd project/nginx-discovery
        sudo docker build . -t nginx-discovery  
        cd reading-env-info
        sudo docker build . -t reading-env-info (NOTE: Only if jenkins setup not work)

    2. Run the nginx container for service discovery
sudo docker run -d -p 80:80 --name nginx-micros nginx-discovery

    3. Prepare environment. Clean the target containers to keep only 1.
        ssh to $service_host
        sudo docker ps -a | grep reading-env-info
        (optional)sudo docker rm -f container-id (ensure keep 1 container alive)
        (optional)sudo docker run -dP reading-env-info:Build2
        sudo docker ps -a | grep reading-env-info (note down the port)

    4. Display the current webpage and note down the current host info and etcd value.
        open http://$service_host:port and note down the host info and etcd info
        open http://$proxy_host/ to ensure it is exactly the same with ralbz001071

    5. Refresh the webpage to ensure no change.
        Reload http://$proxy_host/ several times to ensure no change.

    6. Change the etcd value to show the configuration center.
        curl -X PUT http://$etcd_host/v2/keys/reading-env-info/message -d value="new value"

    7. Add the containers to show the service registration.
        ssh to $service_host
        sudo docker run -dP reading-env-info:Build2
        Run the cmd above several times to get more containers.
        sudo docker ps -a | grep reading-env-info(note down the port)
        open http://$service_host:port and note down the host info
        open http://$proxy_host/ and reload several times to ensure it is exactly the same with ralbz001071

    8. Reduce the containers to show the un-registration.
        ssh to $service_host
        sudo docker ps -a | grep reading-env-info(note down the container ID/name)
        sudo docker rm -f container_ID/name
        repeat several times and ensure at least 1 container left
        sudo docker ps -a | grep reading-env-info(note down the port)
        open http://$service_host:port and note down the host info
        open http://$proxy_host/ and reload several times to ensure it is exactly the same with ralbz001071

NOTE: The interval of refresh is setting as 10 seconds.