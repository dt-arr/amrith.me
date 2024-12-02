---
title: "Kubernetes: Configuring next steps after installing Kubernetes"
description: "This guide will walk you through the process of installing Kubernetes(k8s) on a Raspberry Pi, allowing you to set up a local Kubernetes cluster for learning, development, testing, or training purposes within your own network."
date: 2024-11-21T13:29:58+10:00
draft: true
image: images/icons/rpi-k8s.svg
enableToc: true
enableTocContent: true
tocPosition: inner
author: amrith
---



## Introduction

After you install Kubernetes on your Linux machine, the next steps are to ensure that you configure it to have apps deployed


## Install metrics-server
#### Why the Metrics API Server is Essential for Kubernetes

The Kubernetes Metrics API Server is a useful component that provides real-time insights into resource utilization within your cluster. By collecting and exposing metrics on CPU, memory, and other resources, it empowers you to optimize performance, automate scaling, and make informed decisions.

* Real-time Visibility: Offers up-to-the-minute information on resource consumption for nodes and pods.
* Automated Scaling: Enables automated scaling of applications based on real-time demand.
* Performance Optimization: Helps identify and resolve performance bottlenecks.
* Informed Decision-Making: Provides data-driven insights for better resource allocation and cluster configuration.


#### Download the metrics-server deployment:

````shell
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
````

####
Edit the file to add `--kubelet-insecure-tls` (not recommended in production)

```shell
nano components.yaml
```

``` diff {hl_lines=[10]}
~
    spec:
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
~
````

Apply `components.yaml` file
```shell
kubectl apply -f components.yaml
```


Check if metrics server is running

````shell
kubectl get apiservices
````

{{< expand "kubectl get apiservices Output" >}}

~
````shell
NAME                                   SERVICE                      AVAILABLE   AGE
v1.                                    Local                        True        3d21h
v1.admissionregistration.k8s.io        Local                        True        3d21h
v1.apiextensions.k8s.io                Local                        True        3d21h
v1.apps                                Local                        True        3d21h
v1.authentication.k8s.io               Local                        True        3d21h
v1.authorization.k8s.io                Local                        True        3d21h
v1.autoscaling                         Local                        True        3d21h
v1.batch                               Local                        True        3d21h
v1.certificates.k8s.io                 Local                        True        3d21h
v1.coordination.k8s.io                 Local                        True        3d21h
v1.crd.projectcalico.org               Local                        True        3d14h
v1.discovery.k8s.io                    Local                        True        3d21h
v1.events.k8s.io                       Local                        True        3d21h
v1.flowcontrol.apiserver.k8s.io        Local                        True        3d21h
v1.networking.k8s.io                   Local                        True        3d21h
v1.node.k8s.io                         Local                        True        3d21h
v1.policy                              Local                        True        3d21h
v1.rbac.authorization.k8s.io           Local                        True        3d21h
v1.scheduling.k8s.io                   Local                        True        3d21h
v1.storage.k8s.io                      Local                        True        3d21h
v1alpha1.policy.networking.k8s.io      Local                        True        3d14h
v1beta1.metrics.k8s.io                 kube-system/metrics-server   True        33s
v1beta3.flowcontrol.apiserver.k8s.io   Local                        True        3d21h
v2.autoscaling                         Local                        True        3d21h

~
{{< /expand >}}


Check if you can run the `kubectl top node` command:

```shell
kubectl top node
NAME              CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
rpi5-debian-k8s   132m         3%     1720Mi          21%

```

### OPTIONAL: Change the default KUBE_EDITOR to nano

````shell
export KUBE_EDITOR=nano
````