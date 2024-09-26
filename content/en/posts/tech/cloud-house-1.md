---
title: "Migrating legacy Windows Apps to AWS(or any cloud) with no code change (1/2)"
date: 2019-05-02T13:37:05+11:00
description: "Migrating apps that were designed for older versions of Windows cannot be installed on the recent versions. This limits ones ability to run older apps on newer versions of Windows because older ones reach End of Life. In this post, we check the options available to run older apps on newer versions of Windows"
date: 2019-05-02T12:39:12+11:00
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- windows
- cloudhouse
- migration
categories:
- migration
- cloud
series:
- cloudHouse migration
hideToc: true
enableToc: false
enableTocContent: false
image: images/feature1/windows-xp.png
---

### Introduction
Windows 2008 will reach EOL(End of Life) on January 14, 2020. This means Microsoft will no longer support, add new features or provide security updates to the OS. You may wonder if these OSes are being used and the answer is a whopping YES! There are many organisations who have built and are still using applications on 2008 and 2003 servers (Windows 2003 has been EOL since 2014)

Running production applications on an outdated operating systems is an adventurous thing to do as many new vulnerabilities like Spectre, Meltdown were uncovered in the past which could compromise data. And how can we forget the famous WannaCry that used EternalBlue exploit in SMB of Windows that had widespread impact which caused Microsoft to release updates for even unsupported operating systems. All this means we would need to migrate Apps that are running on 2003/2008 to the latest version of Windows Server. Unfortunately, migrating Apps that were developed at those times cannot be installed on latest version of Windows. They would simply fail to install or not work properly on newer versions on Windows. So, what options have we got?

If the App is too old and not used, it must be decommissioned. If its in use by a large number of people, you may want to refactor to use a newer version of the App if available, or understand the whole requirement around the App and work towards creating a new App altogether. Creating a newer version of the App or redesigning from scratch can be daunting if the number of users is high or if there are many external dependencies on the App. Going back to the problem — All we just want is to make sure the Apps run on newer operating systems. Therefore, lets focus our attention only on that.

The job of the OS is to abstract hardware and serve it for applications. Newer operating systems does the same job with the only difference that they would have improved the Functions that are not compatible with older Applications when it were written. CloudHouse containers resolve the problem of running these old 32-bit Windows XP and 7, Server 2003 and 2008 , and Internet Explorer based applications on modern, secure and supported operating systems and platforms from Microsoft and Citrix.

Reasons why you would package a Windows Application using CloudHouse:

* Your applications are stuck on 2003/2008 or WinXP/Win7 Operating Systems.
* Your applications will not work on 2012/2016/2019 (install / other compatibility issues).
* Some applications may get installed on newer versions but have functions that do not work due to compatibility.

### Packaging
Application packaging is the process of binding the relevant files and components to build a customised application. Almost all softwares that you typically install on Windows are packaged and the installer does the job of deploying it on the target machine which includes copying files, updating registry keys, creating services and starting them.

In our problem we have an App that just wont install or work properly on newer OSes. CloudHouse containerises the App such that it now starts to work on newer systems. This means that legacy App that could not be installed on newer Windows versions can now be installed on latest version of Windows with no code changes. Thus making the legacy App successfully install, run and operate on newer systems.

The most obvious assumption I would make is that these legacy App would be on-prem and using this method you can speed up your Cloud migration.

In a future post, I will show the easy steps to package an App so it would work in newer version of Windows.
