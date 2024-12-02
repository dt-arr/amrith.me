---
title: "Kubernetes Cheat sheets"
description: "Kubernetes Cheat sheets"
date: 2010-12-28T13:37:05+11:00
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- tech
- cheatsheet
categories:
- cheatsheet
hideToc: false
enableToc: true
enableTocContent: false
image: images/icons/gen-cheatsheet-icon.png
tags:
- kubernetes
- Raspberry-Pi
categories:
- kubernetes
- Raspberry-Pi
series:
- kubernetes
- Raspberry-Pi
---



### Namespaces

##### Get namespaces

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl get namespaces
  ```

  {{< /code >}}

  {{< code >}}
  ```shell
Name:         default
Labels:       kubernetes.io/metadata.name=default
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.


Name:         kube-node-lease
Labels:       kubernetes.io/metadata.name=kube-node-lease
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.


Name:         kube-public
Labels:       kubernetes.io/metadata.name=kube-public
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.


Name:         kube-system
Labels:       kubernetes.io/metadata.name=kube-system
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.

  {{< /code >}}
{{< /codes >}}

### Pods

##### Create a new pod with nginx image

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl run nginx --image=nginx
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  pod/nginx created
  ```

  {{< /code >}}
{{< /codes >}}


##### Get pods in default namespace

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl get pods -n default
  ```

  {{< /code >}}
  {{< code >}}

  ```shell
NAME                                                  READY   STATUS    RESTARTS      AGE
nginx                                                 1/1     Running   0             86m
my-otel-demo-accountingservice-5c6578648-7gvvv        1/1     Running   3 (18h ago)   19h
my-otel-demo-adservice-7d6cf47d66-r7tk2               1/1     Running   3 (18h ago)   19h
my-otel-demo-cartservice-54d849946f-nzp5x             1/1     Running   3 (18h ago)   19h
my-otel-demo-checkoutservice-67844cd86-8655c          1/1     Running   3 (18h ago)   19h
my-otel-demo-currencyservice-7b7b799fbb-cxxqb         1/1     Running   3 (18h ago)   19h
my-otel-demo-emailservice-5b6f8fb5cf-t9vkp            1/1     Running   3 (18h ago)   19h
my-otel-demo-flagd-84d895494d-fvmdn                   2/2     Running   6 (18h ago)   19h
my-otel-demo-frauddetectionservice-9b88df59-b88fl     1/1     Running   3 (18h ago)   19h
my-otel-demo-frontend-5c9599c8f8-b6q5d                1/1     Running   3 (18h ago)   19h
my-otel-demo-frontendproxy-64bd649c84-t6h7n           1/1     Running   3 (18h ago)   19h
my-otel-demo-grafana-d888f4886-lgrsh                  1/1     Running   4 (18h ago)   19h
my-otel-demo-imageprovider-c8f796548-jmqjg            1/1     Running   3 (18h ago)   19h
my-otel-demo-jaeger-65748b457d-5pxwf                  1/1     Running   3 (18h ago)   19h
my-otel-demo-kafka-7b6f95d97d-wpvpq                   1/1     Running   3 (18h ago)   19h
my-otel-demo-otelcol-8f467c4d4-vgk7t                  1/1     Running   3 (18h ago)   19h
my-otel-demo-paymentservice-76d994698b-6bmhl          1/1     Running   3 (18h ago)   19h
my-otel-demo-productcatalogservice-749b75b559-vj2l2   1/1     Running   3 (18h ago)   19h
my-otel-demo-prometheus-server-7d8865dc56-4rkrw       1/1     Running   3 (18h ago)   19h
my-otel-demo-quoteservice-669f9578dc-kmnqc            1/1     Running   3 (18h ago)   19h
my-otel-demo-recommendationservice-777fbdd578-pmbn4   1/1     Running   3 (18h ago)   19h
my-otel-demo-shippingservice-676dc67df-mxbg2          1/1     Running   3 (18h ago)   19h
my-otel-demo-valkey-5b9ffb9bd6-2b279                  1/1     Running   3 (18h ago)   19h
otel-demo-opensearch-0                                1/1     Running   3 (18h ago)   19h


  ```
  {{< /code >}}
{{< /codes >}}


##### Get ALL pods in specific namespace

Get status and summary of all running pods in namespace kube-system

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl get pods -n kube-system
  ```

  {{< /code >}}
  {{< code >}}

  ```shell
NAME                                      READY   STATUS    RESTARTS      AGE
calico-kube-controllers-d4dc4cc65-zqcvl   1/1     Running   4 (18h ago)   5d14h
calico-node-llccq                         1/1     Running   4 (18h ago)   5d14h
coredns-7c65d6cfc9-5d457                  1/1     Running   4 (18h ago)   5d21h
coredns-7c65d6cfc9-5lxs8                  1/1     Running   4 (18h ago)   5d21h
etcd-rpi5-debian-k8s                      1/1     Running   4 (18h ago)   5d21h
kube-apiserver-rpi5-debian-k8s            1/1     Running   4 (18h ago)   5d21h
kube-controller-manager-rpi5-debian-k8s   1/1     Running   4 (18h ago)   5d21h
kube-proxy-rpblj                          1/1     Running   4 (18h ago)   5d21h
kube-scheduler-rpi5-debian-k8s            1/1     Running   4 (18h ago)   5d21h
metrics-server-587b667b55-v4kxk           1/1     Running   4 (18h ago)   2d

  ```
  {{< /code >}}
{{< /codes >}}


##### Get status of specific pods

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl get pods otel-demo-opensearch-0 nginx
  ```

  {{< /code >}}
  {{< code >}}

  ```shell

NAME                     READY   STATUS    RESTARTS      AGE
otel-demo-opensearch-0   1/1     Running   3 (18h ago)   19h
nginx                    1/1     Running   0             95m

  ```
  {{< /code >}}
{{< /codes >}}


##### Describe Pod

Get the details of a specific pod

{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl describe pod nginx
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             rpi5-debian-k8s/192.168.1.88
Start Time:       Wed, 27 Nov 2024 11:18:16 +1100
Labels:           run=nginx
Annotations:      cni.projectcalico.org/containerID: fe4b14e2f75d36c9c52f34ce96508ac9e4ba2ff08d7cbffadc9fb2eba5d52bf9
                  cni.projectcalico.org/podIP: 172.16.98.146/32
                  cni.projectcalico.org/podIPs: 172.16.98.146/32
Status:           Running
IP:               172.16.98.146
IPs:
  IP:  172.16.98.146
Containers:
  nginx:
    Container ID:   containerd://9fb9e622e93702b23d91678f892e9ec3fd8e9c353c8db76c9991604ee50bb50f
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:9705bd536e75ac8fc63367571f425def7d1263d6119d02b5d716f80218829c52
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 27 Nov 2024 11:18:27 +1100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jgcrq (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-jgcrq:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
  ```

  {{< /code >}}
{{< /codes >}}


##### Describe pods - Multiple pods
Get the details of a specific pod


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
   kubectl describe pods otel-demo-opensearch-0 nginx
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
Name:             otel-demo-opensearch-0
Namespace:        default
Priority:         0
Service Account:  default
Node:             rpi5-debian-k8s/192.168.1.88
Start Time:       Tue, 26 Nov 2024 16:59:31 +1100
Labels:           app.kubernetes.io/component=otel-demo-opensearch
                  app.kubernetes.io/instance=my-otel-demo
                  app.kubernetes.io/managed-by=Helm
                  app.kubernetes.io/name=opensearch
                  app.kubernetes.io/version=2.18.0
                  apps.kubernetes.io/pod-index=0
                  controller-revision-hash=otel-demo-opensearch-69c87cdd5d
                  helm.sh/chart=opensearch-2.27.0
                  statefulset.kubernetes.io/pod-name=otel-demo-opensearch-0
Annotations:      cni.projectcalico.org/containerID: d144294fffd311010b20aa7c8f47056c360aa9eeb48b9dcbfe6231e782ee9aa7
                  cni.projectcalico.org/podIP: 172.16.98.129/32
                  cni.projectcalico.org/podIPs: 172.16.98.129/32
                  configchecksum: ed1ee46ccf2c0612439b9596039f3f24482737fee8e282809fb4e6a8f417de2
Status:           Running
IP:               172.16.98.129
IPs:
  IP:           172.16.98.129
Controlled By:  StatefulSet/otel-demo-opensearch
Init Containers:
  configfile:
    Container ID:  containerd://0bd0595120fb569e7fe8011debe4209f04c3089eb1d89b07fd089a9dac57ba74
    Image:         opensearchproject/opensearch:2.18.0
    Image ID:      docker.io/opensearchproject/opensearch@sha256:8a354092666b1a47f0172c9c11ed5d6591f7b51843f2f1cebfdcf91032c34bb7
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      #!/usr/bin/env bash
      cp -r /tmp/configfolder/*  /tmp/config/

    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Tue, 26 Nov 2024 18:21:25 +1100
      Finished:     Tue, 26 Nov 2024 18:21:26 +1100
    Ready:          True
    Restart Count:  3
    Environment:    <none>
    Mounts:
      /tmp/config/ from config-emptydir (rw)
      /tmp/configfolder/opensearch.yml from config (rw,path="opensearch.yml")
Containers:
  opensearch:
    Container ID:   containerd://60c757d21713e993a32d8fae8007bc1de0e33d0d22d124abebdc7fd8564619e8
    Image:          opensearchproject/opensearch:2.18.0
    Image ID:       docker.io/opensearchproject/opensearch@sha256:8a354092666b1a47f0172c9c11ed5d6591f7b51843f2f1cebfdcf91032c34bb7
    Ports:          9200/TCP, 9300/TCP, 9600/TCP
    Host Ports:     0/TCP, 0/TCP, 0/TCP
    State:          Running
      Started:      Tue, 26 Nov 2024 18:21:27 +1100
    Last State:     Terminated
      Reason:       Unknown
      Exit Code:    255
      Started:      Tue, 26 Nov 2024 17:55:27 +1100
      Finished:     Tue, 26 Nov 2024 18:20:36 +1100
    Ready:          True
    Restart Count:  3
    Limits:
      memory:  1Gi
    Requests:
      cpu:      1
      memory:   100Mi
    Readiness:  tcp-socket :9200 delay=0s timeout=3s period=5s #success=1 #failure=3
    Startup:    tcp-socket :9200 delay=5s timeout=3s period=10s #success=1 #failure=30
    Environment:
      node.name:                    otel-demo-opensearch-0 (v1:metadata.name)
      discovery.seed_hosts:         opensearch-cluster-master-headless
      cluster.name:                 demo-cluster
      network.host:                 0.0.0.0
      OPENSEARCH_JAVA_OPTS:         -Xms300m -Xmx300m
      node.roles:                   master,ingest,data,remote_cluster_client,
      discovery.type:               single-node
      bootstrap.memory_lock:        true
      DISABLE_INSTALL_DEMO_CONFIG:  true
      DISABLE_SECURITY_PLUGIN:      true
    Mounts:
      /usr/share/opensearch/config/opensearch.yml from config-emptydir (rw,path="opensearch.yml")
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      otel-demo-opensearch-config
    Optional:  false
  config-emptydir:
    Type:        EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:   <unset>
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:          <none>


Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             rpi5-debian-k8s/192.168.1.88
Start Time:       Wed, 27 Nov 2024 11:18:16 +1100
Labels:           run=nginx
Annotations:      cni.projectcalico.org/containerID: fe4b14e2f75d36c9c52f34ce96508ac9e4ba2ff08d7cbffadc9fb2eba5d52bf9
                  cni.projectcalico.org/podIP: 172.16.98.146/32
                  cni.projectcalico.org/podIPs: 172.16.98.146/32
Status:           Running
IP:               172.16.98.146
IPs:
  IP:  172.16.98.146
Containers:
  nginx:
    Container ID:   containerd://9fb9e622e93702b23d91678f892e9ec3fd8e9c353c8db76c9991604ee50bb50f
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:9705bd536e75ac8fc63367571f425def7d1263d6119d02b5d716f80218829c52
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 27 Nov 2024 11:18:27 +1100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jgcrq (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-jgcrq:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
  ```

  {{< /code >}}
{{< /codes >}}


##### Delete the Pod


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl delete pod nginx
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  pod "nginx" deleted

  ```

  {{< /code >}}
{{< /codes >}}

##### Create a Pod using Pod-definition YAML.
   Use an incorrect image to demo the edit functionality later


{{< codes kubectl Output >}}
  {{< code >}}
  ```shell
  kubectl run nginx --image=nginx123 --dry-run=client -o yaml > nginx-definition.yaml
  kubectl create -f nginx-definition.yaml
  ```
  {{< /code >}}


  {{< code >}}

  ```shell
  pod/nginx created
  ```

  {{< /code >}}
{{< /codes >}}




##### Edit the Pod using the Pod-definition YAML.


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
   kubectl edit pods nginx

  
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  
  ~
  spec:
  containers:
  - image: nginx #Edit the pod image specs appropriately (example only)
    imagePullPolicy: Always

  ~
  ```

  {{< /code >}}
{{< /codes >}}

##### Section


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl <command>
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  output here
  ```

  {{< /code >}}
{{< /codes >}}


##### Section


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl <command>
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  output here
  ```

  {{< /code >}}
{{< /codes >}}

##### Section


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl <command>
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  output here
  ```

  {{< /code >}}
{{< /codes >}}



##### Section


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl <command>
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  output here
  ```

  {{< /code >}}
{{< /codes >}}

##### Section


{{< codes kubectl Output >}}
  {{< code >}}

  ```shell
  kubectl <command>
  ```

  {{< /code >}}


  {{< code >}}

  ```shell
  output here
  ```

  {{< /code >}}
{{< /codes >}}

