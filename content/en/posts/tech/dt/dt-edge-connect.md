---
title: "Using Dynatrace EdgeConnect to connect to your private network"
date: 2025-01-24T21:00:56+10:00
draft: true
image: images/icons/dt-migration.png
author: Amrith
authorEmoji: 👨‍💻
tags:
- Dynatrace
- EdgeConnect
categories:
- Dynatrace
series:
- Cloud
---

##### Dynatrace > EdgeConnect

#### Create a new edge connect

#### Enter the IP

#### 
#### Deploy Edge Connect

#### Deploy via Docker > Download the YAML

````yaml
name: amrith-edgeconnect-network
api_endpoint_host: pdd23594.sprint.apps.dynatracelabs.com
oauth:
  client_id: dtxsxx.xxxxxxxx
  client_secret: "#your OAuth client secret"
  resource: urn:dtenvironment:tenantid
  endpoint: https://sso.dynatrace.com/sso/oauth2/token

````

#### Deploy via Docker > Download the YAML
