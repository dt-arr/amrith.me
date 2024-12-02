---
title: "How to: Detect Vulnerabilities in Application using Dynatrace"
description: "Can Dynatrace be used to detect vulnerabilities in Applications? This article shows how it can be done with simple steps."
date: 2022-01-06T09:55:50+11:00
draft: false
image: images/icons/dt-appsec-howto-icon.png
author: Amrith
authorEmoji: 👨‍💻
tags:
- Dynatrace
- AppSec
- cloudcomputing
- AIOps
categories:
- Dynatrace
- Cyber Security
series:
- AppSec
---

*How do you configure Dynatrace to detect vulnerabilities in Applications?*

With Dynatrace installed on your application server, you already get **automated** and **intelligent observability** out of the box with no configurations needed. This means, once you install Dynatrace OneAgent, you will be able to get insights without doing any changes:
- Application Performance Monitoring
- Real User Monitoring
- Service Level Monitoring
- Infrastructure Monitoring

Further, you don’t have to create alerts instead it will automatically identify problems, calculate the business impact and provide a root cause completely automatically through Davis the Dynatrace AI.

## Vulnerabilities in Apps:

[Application software](https://en.wikipedia.org/wiki/Application_software) consists of many programs to serve its purpose and the majority of the programs would have included re-usable components written by various developers from the community. The business logic of the application would therefore depend on these components and thus are sometimes called dependencies. These components could have additional dependencies on libraries and other programs.

If any dependency has a security flaw, it would expose the application and thus finding vulnerabilities in apps is critical. Most importantly, understanding the impact of vulnerability is crucial. For example, if there is a vulnerability in an application that is not connected to the internet or has no data, it probably has low impact and you can deprioritise applying the fix to this system while you patch the most critical application.

## So, how do you find out Vulnerabilities using Dynatrace?

### Pre-requisites:
- The user has Security Admin privileges (see links to instructions at the end of this article).
- Dynatrace OneAgent installed (v1.185+)
- Dynatrace Application Security enabled on your Dynatrace account.
    Go to the Security Overview section to see if Application Security is enabled. If disabled, reach out to the account team.
### My Setup:
- Ubuntu Linux
- Dynatrace OneAgent
- A demo Application: I have used the Sock Shop app, which is an awesome dockerised microservices demo application.

## Enable Runtime vulnerability detection:
To enable Application Security, you need to enable its runtime vulnerability detection functionality.
1. In the Dynatrace menu, go to Application Security > Vulnerabilities and select Activate settings. Alternatively, go to Settings > Application Security.

2. In the Runtime vulnerability detection page that opens, select Enable runtime vulnerability detection.

<img src="/images/techblog/appsec/dt-appsec-how-to-enable-runtime-vul.png" alt="Enable runtime vulnerability detection and enable the supported technolgies"  width=500 height=500 />


## Create a security monitoring rule(optional):
By default, all processes are covered by Runtime vulnerability detection. But let us create rules to enable/disable security monitoring for a certain group of entities. These rules can be applied to:
- Management Zone
- Process Tag
- Host Tag
In the below example, I have enabled Monitoring on all management zones except my Sandbox environment. You could change this based on your needs.

<img src="/images/techblog/appsec/dt-appsec-how-to-enable-runtime-mon-rule.png" alt="Create an optional security monitoring rule"  width=500 height=500 />

These settings are available in the [Dynatrace menu](https://www.dynatrace.com/support/help/get-started/navigation), go to **Settings** > **Application Security and select Security-Monitoring rules**.

## Enable OneAgent reporting of software components
After enabling runtime vulnerability detection, you need to enable OneAgent reporting software components from your running applications to Dynatrace. There are two options: Globally and at the Process group level. I will be applying the setting Globally in my example.
1. In the Dynatrace menu, go to Settings and select Server-side service monitoring > Deep monitoring.
2. Scroll down to New OneAgent features and expand that section.
3. On the Global tab, filter for Reporting.
4. Enable each software component reporting feature that you want to include in vulnerability detection globally.
5. Select Save changes to save your configuration.

<img src="/images/techblog/appsec/dt-appsec-how-to-soft-comp-reporting.png" alt="Enable software component reporting that you like."  width=500 height=500 />

That’s it!

Based on the security-monitoring rules, Dynatrace would now detect vulnerabilities in applications where this software and technologies are installed.

Vulnerabilities detected by Dynatrace on the demo application installed on the Linux machine
In the next article, I will show how these vulnerabilities are seen in Dynatrace and how **DSS(Davis Security Score)** uses the **Common Vulnerability Scoring System (CVSS)** to provide a more accurate risk assessment

## Further reading:
Next article: Understanding the precise risk of a vulnerability in an environment with Dynatrace: [URL](https://medium.com/intelligent-observer/awesome-insights-on-vulnerabilities-in-applications-using-dynatrace-cvss-and-dss-a1d08f2f9fc1)
Application Security prerequisites: [URL](https://www.dynatrace.com/support/help/shortlink/start-security#prerequisites)
Security Monitoring rules: [URL](https://www.dynatrace.com/support/help/shortlink/appsec-mon-rules)
Assigning Security Admin permissions in Dynatrace: [URL](https://www.dynatrace.com/support/help/shortlink/start-security#assign-permissions)

Note: This article was first published on [Medium](https://medium.com/intelligent-observer/how-to-detect-vulnerabilities-in-application-using-dynatrace-a84239a7d430) by the same author