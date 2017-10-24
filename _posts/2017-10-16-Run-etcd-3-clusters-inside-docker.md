---
layout: post
title:  "Run etcd 3 clusters inside docker"
categories: Micro_Services
tags:  docker etcd
author: Internet
---
{:toc}

In order to expose the etcd API to clients outside of Docker host, use the host IP address of the container. Please see docker inspect for more detail on how to get the IP address. Alternatively, specify ```--net=host``` flag to docker run command to skip placing the container inside of a separate network stack.

### Running a single node etcd

Use the host IP address when configuring etcd:

    export NODE1=192.168.1.21

Run the latest version of etcd:  

    docker run \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=${DATA_DIR}:/etcd-data \
    --name etcd quay.io/coreos/etcd:latest \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name node1 \
    --initial-advertise-peer-urls http://${NODE1}:2380 --listen-peer-urls http://${NODE1}:2380 \
    --advertise-client-urls http://${NODE1}:2379 --listen-client-urls http://${NODE1}:2379 \
    --initial-cluster node1=http://${NODE1}:2380

List the cluster member:  

    etcdctl --endpoints=http://${NODE1}:2379 member list

### Running a 3 node etcd cluster  

    # For each machine
    ETCD_VERSION=latest
    TOKEN=my-etcd-token
    CLUSTER_STATE=new
    NAME_1=etcd-node-0
    NAME_2=etcd-node-1
    NAME_3=etcd-node-2
    HOST_1=10.20.30.1
    HOST_2=10.20.30.2
    HOST_3=10.20.30.3
    CLUSTER=${NAME_1}=http://${HOST_1}:2380,${NAME_2}=http://${HOST_2}:2380,${NAME_3}=http://${HOST_3}:2380
    DATA_DIR=/var/lib/etcd

    # For node 1
    THIS_NAME=${NAME_1}
    THIS_IP=${HOST_1}
    docker run \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=${DATA_DIR}:/etcd-data \
    --name etcd quay.io/coreos/etcd:${ETCD_VERSION} \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}

    # For node 2
    THIS_NAME=${NAME_2}
    THIS_IP=${HOST_2}
    docker run \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=${DATA_DIR}:/etcd-data \
    --name etcd quay.io/coreos/etcd:${ETCD_VERSION} \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}

    # For node 3
    THIS_NAME=${NAME_3}
    THIS_IP=${HOST_3}
    docker run \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=${DATA_DIR}:/etcd-data \
    --name etcd quay.io/coreos/etcd:${ETCD_VERSION} \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}

### To run etcdctl using API version 3:  

    docker exec etcd /bin/sh -c "export ETCDCTL_API=3 && /usr/local/bin/etcdctl put foo bar"
 

### Mounting a certificate volume

The etcd release container does not include default root certificates. To use HTTPS with certificates trusted by a root authority (e.g., for discovery), mount a certificate directory into the etcd container:  

    docker run \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=/etc/ssl/certs/ca-certificates.crt:/etc/ssl/certs/ca-certificates.crt \
    quay.io/coreos/etcd:latest \
    /usr/local/bin/etcd --name my-name \
    --initial-advertise-peer-urls http://localhost:2380 --listen-peer-urls http://localhost:2380 \
    --advertise-client-urls http://localhost:2379 --listen-client-urls http://localhost:2379 \
    --discovery https://discovery.etcd.io/86a9ff6c8cb8b4c4544c1a2f88f8b801

### Put and get the key value from etcd  

     curl -X PUT http://${HostIP}:2379/v2/keys/message -d value="Hello"
     curl http://${HostIP}:2379/v2/keys/message

From - <https://coreos.com/etcd/docs/latest/op-guide/container.html#docker>

### NOTE:
In case hit "cannot assign requested address" issue, please change the "listen-peer-urls" and "listen-client-urls" to 0.0.0.0:2379 or 0.0.0.0:2380. But it will raise a warning -  embed: serving insecure client requests on [::]:2379, this is strongly discouraged!

    docker run -p 2379:2379 -p 2380:2380 --volume=/micros/micros/volume/etcd/node1:/etcd-data --name etcd quay.io/coreos/etcd:latest /usr/local/bin/etcd --data-dir=/etcd-data --name node1 --initial-advertise-peer-urls http://9.37.1.71:2380 --listen-peer-urls http://0.0.0.0:2380 --advertise-client-urls http://9.37.1.71:2379 --listen-client-urls http://0.0.0.0:2379 --initial-cluster node1=http://9.37.1.71:2380
