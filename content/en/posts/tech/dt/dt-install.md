---
title: "Dynatracing a Linux server in under 5 minutes"
date: 2021-06-21T13:37:05+11:00
description: "In this blog, I am going to show you how easy it is to setup a Dynatrace account and get the Dynatrace agent installed and use the power of AI-Ops"
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- Install
- Dynatrace
categories:
- AIOps
- Dynatrace
series:
- Dynatrace
hideToc: true
enableToc: false
enableTocContent: false
image: images/icons/dt-install-trace.svg
---
#### Dynatrace: Signing up

* Sign up for <a href="https://www.dynatrace.com/trial/" target="_blank">Dynatrace Trial</a>
* Use your email, password, Name etc.
* Select your region:

<img src="/images/dt/dt-install-region-selector.png" alt="image alt"  width=500 height=500 />
 
 * Get the Welcome to Dynatrace page:

 <img src="/images/dt/dt-install-welcome-page.png" alt="image alt"  width=500 height=500 />
 #### 

* Go to Dynatrace Hub

 <img src="/images/dt/dt-install-dt-hub.png" alt="image alt"  width=500 height=500 />

 * Download the OneAgent for Linux

 <img src="/images/dt/dt-install-download-linux-agent.png" alt="image alt"  width=500 height=500 />

* Install OneAgent on a Linux machine

<img src="/images/dt/dt-install-agent-install.gif" alt="image alt"  width=500 height=500 />

<img src="/images/dt/dt-install-agent-install-2.gif" alt="image alt"  width=500 height=500 />



#### Dynatracing the server:

Dynatrace would automatically detect the technologies, process, services, application, logs, metrics, topologies, events and lot more in the matter of minutes:

<img src="/images/dt/dt-install-host-in-DT.gif" alt="image alt"  width=500 height=500 />

<img src="/images/dt/dt-install-host-in-DT-2.gif" alt="image alt"  width=500 height=500 />

#### AIOps in action:

With no additional configuration, Dynatrace is able to automatically detect anomalies, report affected apps, services, infrastructure, calculate business impact and finally identify root cause.

<img src="/images/dt/dt-install-infra-problems.png" alt="image alt"  width=500 height=500 />

### Dynatrace OneAgent installed as a Docker container

{{< highlight shell>}}

sudo docker run -d --restart=unless-stopped --privileged=true --pid=host --net=host --ipc=host -v /:/mnt/root -e ONEAGENT_INSTALLER_SCRIPT_URL=https://<tenant-id>.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?arch=x86 -e ONEAGENT_INSTALLER_DOWNLOAD_TOKEN=████.█████████████████████████.█████████████████████████████████ dynatrace/oneagent --set-app-log-content-access=true


{{< /highlight >}}

<img src="/images/dt/dt-install-docker-container.gif" alt="image alt"  width=500 height=500 />

### Installing Dynatrace in a Kubernetes Cluster 

{{< highlight shell>}}

wget https://github.com/dynatrace/dynatrace-operator/releases/latest/download/install.sh -O install.sh && sh ./install.sh --api-url "https://<tenant-id>.live.dynatrace.com/api" --api-token "████.█████████████████████████.█████████████████████████████████" --paas-token "████.█████████████████████████.█████████████████████████████████" --skip-ssl-verification --cluster-name "k8s-test"

{{< /highlight >}}

<img src="/images/dt/dt-install-k8s.gif" alt="image alt"  width=500 height=500 />


##### Automatic container, process, techology, topology view and AI Ops in Dynatrace:

<img src="/images/dt/dt-install-dt-ui-after-container.gif" alt="image alt"  width=500 height=500 />


