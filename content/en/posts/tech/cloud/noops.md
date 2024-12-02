---
title: "Eliminating toil in the cloud with NoOps automation"
date: 2020-08-18T13:37:05+11:00
description: "What is Toil? Am I affected by Toil? Why is it important for an SRE to eliminate Toil? In this post, I explain how to identify toil and how to eliminate them through NoOps practices"
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- SRE
- NoOps
categories:
- SRE
- NoOps
series:
- SRE
hideToc: true
enableToc: false
enableTocContent: true
image: images/feature1/operations.png
---
A wildlife videographer typically returns from a shoot with hundreds of gigabytes of raw video files on 512GB memory cards. It takes about 40 minutes to import the files into a desktop device, including various prompts from the computer for saving, copying or replacing files. Then the videographer must create a new project in a video-editing tool, move the files into the correct project and begin editing. Once the project is complete, the video files must be moved to an external hard drive and copied to a cloud storage service.

All of this activity can be classified as toil — manual, repetitive tasks that are devoid of enduring value and scale up as demands grow. Toil impacts productivity every day across industries, including systems hosted on cloud infrastructure. The good news is that much of it can be alleviated through automation, leveraging multiple existing cloud provider tools. However, developers and operators must configure cloud-based systems correctly, and in many cases these systems are not fully optimised and require manual intervention from time to time.

##  Identifying toil
Toil is everywhere. Let’s take Amazon EC2 as an example. EC2 provides Amazon Elastic Block Store (EBS) compute and storage capacity to build servers in the cloud. The storage units associated with EC2 are disks which contain operating system and application data that grows over time, and ultimately the disk and the file system must be expanded, requiring many steps to complete.

The high-level steps involved in expanding a disk are time consuming. They include:

1. Get an alert on your favourite monitoring tool
2. Identify the AWS account
3. Log in to the AWS Console
4. Locate the instance
5. Locate the EBS volume
6. Expand the disk (EBS)
7. Wait for disk expansion to complete
8. Expand the disk partition
9. Expand the file system

One way to eliminate these tasks is by allocating a large amount of disk space, but that wouldn’t be economical. Unused space drives up EBS costs, but too little space results in system failure. Thus, optimising disk usage is essential.

This example qualifies as toil because it has some of these key features:

1. The disk expansion process is managed manually. Plus, these manual steps have no enduring value and grow linearly with user traffic.
2. The process will need to be repeated on other servers as well in the future.
3. The process can be automated, as we will soon learn.

## The move to NoOps

Traditionally, this work is performed by IT operations, known as the Ops team. Ops teams come in variety of forms but their primary objective remains the same – to ensure that systems are operating smoothly. When they are not, the Ops team responds to the event and resolves the problem.

NoOps is a concept in which operational tasks are automated, and there is no need for a dedicated team to manage the systems. NoOps does not mean operators would slowly disappear from the organisation, but they would now focus on identifying toil, finding ways to automate the task and, finally, eliminating it. Some of the tasks driven by NoOps require additional tools to achieve automation. The choice of tool is not important as long as it eliminates toil.

{{< img src="/images/eliminating-toil/NoOps-approach-in-responding-to-an-alert-in-the-system.png" title="Figure 1" caption="NoOps Approach in responding to an alert in the system" alt="image alt" >}}

In our disk expansion example, the Ops team typically would receive an alert that the system is running out of space. A monitoring tool would raise a ticket in the IT Service Management (ITSM) tool, and that would be end of the cycle.

Under NoOps, the monitoring tool would send a webhook callback to the API gateway with the details of the alert, including the disk and the server identifier. The API gateway then forwards this information and triggers Simple Systems Manager (SSM) automation commands, which would increase the disk size. Finally, a member of the Ops team is automatically notified that the problem has been addressed.

##   AWS Systems Manager automation

The monitoring tool and the API gateway play an important role in detecting and forwarding the alert, but the brains of NoOps is AWS Systems Manager automation.

This service builds automation workflows for the nine manual steps needed for disk expansion through an SSM document, a system-readable instruction written by an operator. Some tasks may even involve invoking other systems, such as AWS Lambda and AWS Services, but the orchestration of the workflow is achieved by SSM automation, as shown in this table:
 

   Step #   | Task Name                             | SSM Automation Action         | Comments   
--------    |--------------------------------------|-------------------------------|----------
1           | Get trigger details and expand volume	| aws:invokeLambdaFunction	    | Using Lambda, the system must determine the exact volume and expand it based on a pre-defined percentage or value.
2           | Wait for the disk expansion	        | aws:waitUntilVolumeIsOkOnAws  | Disk expansion would fail if it goes to the next steps without waiting for time to complete.
3           | Get OS information	                | aws:executeAwsApi             | Windows and Linux distros have different commands to expand partition and file systems.
4           | Branch the workflow depending on the OS| aws:branch                   | The automation task would now be branched based on the OS.
5           | Expand the disk	                    | aws:runCommand	            | The branched workflow would run commands on the OS that would expand the disk gracefully.
6           | Send notification to the ITSM tool	| aws:invokeLambdaFunction	    | Send a report on the success or failure of the NoOps task for documentation.

## Applying NoOps across IT operations

This example shows the potential for improving operator productivity through automation, a key benefit of AWS cloud services. This level of NoOps can also be achieved through tools and services from other cloud providers to efficiently operate and secure hybrid environments at scale. For AWS deployments, Amazon EventBridge and AWS Systems Manager OpsCenter can assist in building event-driven application architectures, resolving issues quickly and, ultimately, and eliminating toil.

Other NoOps use cases include:

* Automatically determine the cause of system failures by extracting the appropriate sections of the logs and appending these into the alerting workflow.
* Perform disruptive tasks in bulk, such as scripted restart of EC2 instances with approval on multiple AWS accounts.
* Automatically amend the IPs in the allowlist/denylist of a security group when a security alert is triggered on the monitoring tool.
* Automatically restore data/databases using service requests.
* Identify high CPU/memory process and kill/restart if required automatically.
* Automatically clear temporary files when disk utilization is high.
* Automatically execute EC2 rescue when an EC2 instance is dead.
* Automatically take snapshots/Amazon Machine Images (AMIs) before any scheduled or planned change.

In the case of the wildlife videographer, NoOps principles could be applied to eliminate repetitive work. A script can automate the processes of copying, loading, creating projects and archiving files, saving countless hours of work and allowing the videographer to focus on core aspects of production.

For cloud architectures, NoOps should be seen as the next logical iteration of the Ops team. Eliminating toil is essential to help operators focus on site reliability and improving services.

{{< notice info >}}
This post was first published in [DXC Blogs](https://blogs.dxc.technology/2020/08/18/eliminating-toil-in-the-cloud-with-noops-automation/) on 18 August 2020 written by the same author {{< /notice >}}

## See NoOps in Action

{{< youtube Qs-xwf5Q4aM >}}