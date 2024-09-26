---
title: "Patching immutable and in-place instances on AWS"
date: 2019-05-29T00:04:50+09:00
publishDate: 2019-05-29
description:
tags:
- ec2
- aws
- patching
- immutable architectures
series:
-
categories:
-
---


#### Video

{{< youtube atcuP5Z-wuU >}}


#### Description
EC2 instances can be launched in seconds with a few clicks. Some instances have short lifespan and gets terminated in few hours or days, while some instances live for a longer period of time - months or years. All instances that are running must have their OS updated regularly. Vulnerabilities such as Heartbleed, Spectre and Meltdown reminds us that patch management is a crucial component of operational excellence. Immutable architectures are also prone to vulnerabilities if instances are not replaced or updated regularly.

In this session we uncover the various lifecycles of an EC2 instances and approaches we can take in ensuring that they are patched for all situations. We talk about in-place patching using AWS Systems Manager and we also talk about situations where immutable EC2 instances can be at risk of not being patched and how to overcome it using AWS Step Functions.

#### Presented by
* Amrith Raj Radhakrishnan
* Sudev Kuru

#### Presented in
* Melbourne

<br>

---