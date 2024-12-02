---
title: "Managing Cloud Outages: A pessimist's way to architect resilient infrastructures"
description: "Architecting resilient infrastructure to function during a Cloud Outage"
date: 2016-04-25T13:12:41+11:00
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- aws
- gcp
- cloudcomputing
categories:
- aws
- gcp
series:
- aws
hideToc: true
enableToc: false
enableTocContent: false
image: images/feature1/under-construction.png
---


Google Cloud Platform suffered an [outage](https://status.cloud.google.com/incident/compute/16007) for a total of 18 minutes on 11th April 2016 when Google Compute Engine instances in all regions lost external connectivity. Yes, you read it right - all regions! This probably isn't the first time when a major Cloud Service provider suffered large scale outage. Amazon suffered a similar outage in September 2015 as well which affected a lot of services including Netflix, IMDB and Amazon's Instant Video and Book sites.

We all need to and must place workloads to Cloud eventually. This is not a suggestion I am recommending but I would mandate it because of all the good things that Cloud has to offer. Am I recommending to move workloads at the cost of the core business services? Absolutely no! You would need to architect your solution in a way that ensures your core services are always running even if there is a storm sweeping the Cloud ecosystem.

If most of the services are hosted on the Private Cloud, you would need to ensure that it fails over to a Public Cloud should there be an outage in your datacenter. Like having the primary on-premise and the disaster recover site on the Cloud under a Global Load Balancer. An outage need not be a complete shutdown of the on-premise Private Cloud infrastructure. You could also use your DR-on-the-cloud to react when there is high usage in your local datacenter and add capacity on the Cloud under the same Global Load Balancer. On AWS, one could use a Custom Metric and CloudWatch alarm to monitor say for example local Web servers and when the usage hits a value beyond a defined threshold, you could drive an Autoscaling policy to add instances on AWS thus preventing a performance outage to your clients.

Small and medium businesses might not have an on-premise infrastructure in the first place and may have almost all the workloads on the Cloud. Ensuring that these are on resilient systems has to be done on various aspects. For example, if you have clients globally you would need to ensure that the data are properly replicated between Cloud Regions(geographies). For a client base which is local you can keep your data spread between multiple availability zones. But the question is, is this all enough? What if a Cloud Service which is global, like authentication, fails? Even if you have data available in multiple geographies, your service would probably be unavailable. This is a nightmare that you should be prepared for and a proper design could minimize the impact.

Just like Cloud Service providers use Power, Internet from multiple service providers to manage their datacenter/infrastructure. Using multiple Cloud service providers can be one of the Solutions. Cloud clients should take advantage of multiple cloud providers and design their application/services in a way that even when there is an outage in Cloud Service of one of the provider their services should be able to run from another service provider. For example, you could ensure your Google Cloud Storage bucket is in sync with AWS S3 buckets, this can be achieved this by setting up periodic synchronization from S3 to Google Cloud Storage with advanced filters based on file creation dates, file-name filters, and the times of day you prefer to import data. With this data you would be able to have a version of your application be made available through Google Cloud Platform and get this ready when there be an outage in AWS.

Software updates, new features, bug fixing is a continuous process and would never stop and these drive controlled Change requests which does gets out of hand every now and then. Things that could fail will fail.

Google provided credits to their users affected by the outage but that does not save from the loss of money, business or reputation one may had to face to with their clients.

Architecture plays an important role for availability and should start from low level design like a two node cluster to all the way to multiple Cloud Service Providers. Planning ahead for large scale Cloud incidents can be done by architecting on multiple providers or infrastructures which will minimize the impact.

So, would you know when would the next outage be and how have you prepared for it...?