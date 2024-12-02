---
title: "Understanding the precise risk of a vulnerability in an environment with AI"
description: "Finding vulnerabilities is always one side of the story. Risk assessments should always be based on environmental characteristics like where the impacted system is and what access the vulnerable system has. This blog will look at how the AI of Dynatrace accounts for the environmental characteristics for given vulnerabilities to rate the risk level accurately. This will help teams to prioritise where to start the remediation."
date: 2022-01-07T09:56:08+11:00
draft: false
image: images/icons/dt-appsec-dss-and-cvss-icon.png
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

*How are environmental characteristics accounted for when analysing vulnerabilities?*

In the previous blog, we looked at the simple steps to enable Application security in Dynatrace. In this article, we will look at how the AI accounts for the environmental characteristics for given vulnerabilities to accurately rate the risk level.

## Analysing Vulnerabilities:

With Application Security enabled and configured, Dynatrace will report vulnerabilities analysing additional contextual information like exposure to the internet, access to sensitive data(DB) etc.
Needless to say, this is in addition to Infrastructure, Application, Real User Monitoring that Dynatrace already provides.
Below is a screenshot of the Application Security Overview page:

<img src="/images/techblog/appsec/dt-appsec-dss-overview-page.png" alt="Application Security Overview Page"  width=500 height=500 />

By clicking on *View all vulnerabilities* button, you would see all the vulnerabilities. You can even filter the vulnerabilities seen on a specific host. In the below example, I have looked up vulnerabilities in the specific host based on the hostname:

<img src="/images/techblog/appsec/dt-appsec-dss-view-all-vul.png" alt="View all vulnerabilities"  width=500 height=500 />

Let’s take a look into the vulnerability. You may want to open the image in a new tab to see it on full screen.

<img src="/images/techblog/appsec/dt-appsec-dss-vul-details.png" alt="Vulnerability: Context, details, vulnerable component, Davis Security Score, CVSS, Problem evolution, related entities, container images and process."  width=500 height=500 />

The above screenshot shows information not just about the vulnerability but most importantly it tells me that:

* there may or may not be public exposure(more accurately in the next sections),
* the vulnerability is not affecting any sensitive data and
* the vulnerable functions are not in use.

## Davis Security score(DSS):

Dynatrace has calculated a *Davis Security score* for this vulnerability which is an enhanced risk-calculation score based on the industry-standard [Common Vulnerability Scoring System](https://en.wikipedia.org/wiki/Common_Vulnerability_Scoring_System). Because Davis AI also considers parameters like public internet exposure and checks to see if and where sensitive data is affected, **DSS is the most precise risk-assessment score available.**

**DSS is more accurate**: Davis doesn’t assume the worst-case scenario. Instead, Davis adapts the characteristics of the vulnerability to your particular environment, taking into consideration its structure and topology, and advises you as to which elements are prone to errors and how to handle security issues. With Davis AI, you can find out if the affected entity is reachable from the Internet and if there is any data stored in reach of an affected entity.

**DSS makes you more efficient**: By including additional parameters in its analysis, Davis can more precisely calculate the security score and predict the potential risk of a vulnerability to your environment. By reducing the score of vulnerabilities that are, in fact, not critical for your environment, you gain time to focus on the real issues and fix them faster.

#### Why does the same vulnerability have different DSS scores?

The table below shows how DSS provides an accurate assessment of the Log4j vulnerability based on the environment of the affected system. Although the [CVSS score for the vulnerability is 9.8](https://nvd.nist.gov/vuln/detail/CVE-2019-17571), DSS doesn't assume the worst-case scenario and does a true assessment.


<img src="/images/techblog/appsec/dt-appsec-dss-vs-csss-table.png" alt="DSS accounts environmental characteristics for the Log4j Deserialisation of untrusted data vulnerability"  width=500 height=500 />

In the next section, I will show you how each of the scenarios looks like and how you could use DSS for precise risk assessment.

## Log4j
In the same Linux machine, I installed a Java Application that used the infamous Log4j library for logging. Within no time, I see Dynatrace detected and rated the vulnerability as critical:

<img src="/images/techblog/appsec/dt-appsec-dss-1-critical-vul.png" alt="Dynatrace detecting log4j in a Java App"  width=500 height=500 />

Note in the below screenshot, Davis has marked that the vulnerability with symbols to indicate that it has access to sensitive data and there is a known malicious code that exploits this vulnerability.

<img src="/images/techblog/appsec/dt-appsec-dss-log4j-icons.png" alt="DSS Scores and the Public exposure, Sensitive data, Vulnerable functions and if it is Public exploit"  width=500 height=500 />

Note that this time, we see that this vulnerability has the sensitive data assets symbol enabled and it has a critical DSS score.

## Same Vulnerability but varying DSS depending on exposure and sensitivity

In the below example we see a score of 9.8 which matches the CVSS base score but you could see that the exposure to other internet was not determined but sensitive data was within range

## Scenario 1 

Log4j Vulnerability with sensitive data assets and undetermined public exposure: Critical risk(score 9.8)

* Public internet exposure: Not determined
* Sensitive data assets: Within range
* CVSS Score: 9.8 
* DSS Score: <mark> 9.8 (critical risk)</mark> DSS is unchanged because of undetermined public internet exposure

<img src="/images/techblog/appsec/dt-appsec-scenario-1.png" alt="Scenario-1: Vulnerability with sensitive data assets and undetermined public exposure: Critical risk(score 9.8)"  width=500 height=500 />

## Scenario 2

Log4j Vulnerability with sensitive data assets and exposure to Adjacent network: High risk(score 8.8)

* Public internet exposure: Adjacent network
* Sensitive data assets: Within range
* CVSS Score: 9.8 (no change)
* DSS Score: <mark> 8.8 (critical risk)</mark> score less than CVSS

In the below example for the same vulnerability, Davis has lowered the score because the attack vector(exposure) is an Adjacent network. An adjacent network means the attacker must be on the same network. The sensitive data assets are within range and it is still a high risk.

<img src="/images/techblog/appsec/dt-appsec-scenario-2.png" alt="Scenario-2: Davis AI automatically lowers the score to reflect the exposure of the vulnerability"  width=500 height=500 />

## Scenario 3 

Log4j Vulnerability with sensitive data assets not in range and exposure to Adjacent network: High risk(score 7.6)

* Public internet exposure: adjacent network
* Sensitive data assets: Not within range
* CVSS Score: 9.8 (no change)
* DSS Score: <mark> 7.6 (high risk)</mark> score less than CVSS

In the next sample scenario, we see that the exposure is to an adjacent network and sensitive data is not within range further lowering the DSS

<img src="/images/techblog/appsec/dt-appsec-scenario-3.png" alt="Scenario-3: Davis AI automatically lowers the score to reflect the exposure of the vulnerability"  width=500 height=500 />

## Scenario 4

Log4j Vulnerability with sensitive data assets within range and exposure to Adjacent network: Critical risk(9.8)

* Public internet exposure: Public network
* Sensitive data assets: Within range
* CVSS Score: 9.8 
* DSS Score: <mark> 9.8 (critical risk)</mark> score equals CVSS because of Public exposure

In the below example, the vulnerability is exposed to Public network and has sensitive data within range. Because of this, the DSS score is 9.8 and is categorised as Critical risk.

<img src="/images/techblog/appsec/dt-appsec-scenario-4.png" alt="Scenario-4: Davis AI automatically has detected the exposure to Public network and sensitive data access within range marking the risk level to Critical and a DSS score to 9.8"  width=500 height=500 />

## Why is DSS important?

In the above example, we looked at the same log4j vulnerability which has a CVSS score of 9.8, but because Davis AI considers environmental characteristics like public internet exposure and checks to see if and where sensitive data is affected, DSS is the most precise risk-assessment score available.

With a lowering score(scenarios 2 and 3) and an explanation that the attacker needs to be on the same network, you can focus on **securing the network layer quickly while you work out a rollout of the patch to fix the vulnerability.**

With a high score(scenario 4) and an explanation that the attacker can be on any public network, you would have to prioritise restricting the network access, securing the data and applying a patch.

## Does the AI have enough data to calculate this accurately?

Dynatrace already has the full topology information of the application and you can verify this in the Smartscape topology. In addition, all the individual distributed transaction tracing (PurePath) looks for everything in the request from the network to code-level to calculate DSS.

<img src="/images/techblog/appsec/dt-appsec-PurePath.png" alt="Smartscape showing all the topological dependencies in the infrastructure, processes, and services in real-time of our Linux server"  width=500 height=500 />


Below screenshots show the Backtrace of a request where you can identify the source IPs of the client connecting. In the first screenshot, you see a private IP while in the second you see the Public IPs.

#### Clients connected from Private IPs
<img src="/images/techblog/appsec/dt-appsec-backtrace-private-ips.png" alt="A backtrace of PurePath(Distributed trace) showing the client IPs. Note that these are coming from a private range 192.168.x.x which Davis reads and concludes that the exposure is adjacent network."  width=500 height=500 />

#### Clients connected from Public IPs
<img src="/images/techblog/appsec/dt-appsec-backtrace-public-ips.png" alt="A backtrace of PurePath(Distributed trace) showing the client IP. Note that these are coming from the Public internet which Davis reads and concludes that the exposure is public network.."  width=500 height=500 />

## Conclusion

It took very few clicks to enable run-time vulnerability detection and Dynatrace was able to automatically detect, categorise, calculate DSS(Davis Security Score) and provide impact for the vulnerability.

We were also able to see how the data that DSS uses is always in context and why DSS is more reliable and useful in the real world than just CVSS. Just because you have vulnerable software doesn’t mean you need to patch all of them together. Using DSS, you can now make an accurate risk assessment of your entire environment and work out a workable mitigation plan.

## Further reading

Previous Blog: How to detect Vulnerabilities in Application using Dynatrace: [URL](https://medium.com/intelligent-observer/how-to-detect-vulnerabilities-in-application-using-dynatrace-a84239a7d430)
Davis Security Score calculations: [URL](https://www.dynatrace.com/support/help/shortlink/dss)
Adjacent Network and CVSS Scoring: [URL](https://www.first.org/cvss/examples)
CVSS Scoring: [URL](https://nvd.nist.gov/vuln-metrics/cvss)
SmartScape Topology: [URL](https://www.dynatrace.com/support/help/shortlink/smartscape-topology-hub)
PurePath: [URL](https://www.dynatrace.com/support/help/shortlink/services-purepath)
Davis AI: [URL](https://www.dynatrace.com/ai/)

Note: This article was first published on [Medium](https://medium.com/intelligent-observer/awesome-insights-on-vulnerabilities-in-applications-using-dynatrace-cvss-and-dss-a1d08f2f9fc1) by the same author