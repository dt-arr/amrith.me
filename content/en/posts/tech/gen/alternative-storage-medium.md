---
title: "Desperate need for alternative storage medium"
description: "Enterprise storage arrays still uses Hard disk drives and Solid state drives even today and there is a need for an alternative storage medium to cater for the next century"
date: 2014-02-04T12:39:12+11:00
draft: false
author: Amrith
authorEmoji: 👨‍💻
tags:
- storage
- san
categories:
- storage
- san
hideToc: true
enableToc: false
enableTocContent: false
image: images/feature2/transfer.png
---
A lot of my friends reading this would argue that there is already awesome, high performance storage available from EMC, HDS and/or NetApp. If they happen to be a EMC fan they would point to VMAX or VSP if they are a HDS fan. I am not overly happy with the way these storage have been performed compared to the Compute counterparts.

Can you guess what kind of storage does a super computer use?
A EMC VMAX or HDS VSP or a NetAPP FAS6200? Probably or probably not:
Do direct me if you find use cases of any of these products for HPC(High performance computing).

As the storage medium still remain hard disks or to a certain extent Flash drives(very expensive), purchasing the above storage array might satisfy the need for capacity but from a performance perspective they might not be able to deliver. In an enterprise storage array, Flash drive's capacity is low. So, even if one assumes to have sufficient capital to invest in Flash drives, we might end up filling up the whole array without even accomplishing a Petabyte of storage. So, how do we manage performance and capacity at the same time?

Auto-tiering can be an option but this might not be the solution as there is another layer of bottle neck in the front-end adapters whose throughput are limited. Also, the ratio of high-performance disk plays a vital role in cases where the 'hot data' is more than the available High-performance disks.

There is a company named DataDirect Networks(DDN) that manufactures high density - high performance storage arrays. They are in the HPC arena, so unless any of our clients work for the major stock exchange or a research company, the chances of working on these arrays is slightly low.

Here's what it does to super-boost performance: Have high density enclosures. This means, they are going to rack and stack more drives per enclosure, and hence more storage per rack. That does compensate capacity but for performance they believe there is no super powered storage medium yet that could improve performance and hence depend on the data striping to increase the IOPS and hence improve performance and capacity at the same time. The bottleneck that could be caused for Front end adapters are replaced by 16 x 56Gb/s Infiniband ports (VMAX has Max. 10Gb/s FCoE ports, 16 Gb/s of FC would be possible in the near future). Further reading: DDN SFA 12K.


Acknowledging Moore's law, 'the number of transistors on integrated circuits doubles approximately every two years', there is no such prophecy or even a satisfactory alternative theory or promise that could say that the data storage technology would be able to satisfy the current data growth.

To understand the depth of the subject it is quite important to know the fundamentals of data storage. I am going to rush through all of them in a glance. First of all, the need for binary. We use Binary system because it was easy to store(and many other reasons) two states: 0 or 1. In electronics this actually relates to positive or negative (magnetic flux in the Hard disk drive or the tape) or pits and lands in the field of optical medium(CD/DVD/Blu-ray) or the states of cells in Single or Multi Level Cell (SLC/MLC) for Flash Memory.

Let's wind the clock for few decades when digital data was absent commercially. But we still watched movies and spoke over the phone.

Movies and music were stored in magnetic tapes. We used Video Cassette Player(VCP/VCR) to view them. Automated long distance telephone calls were possible without the need for an operator to perform the switching. Basically, everything were analog and the transformation to digital lead to the need for search for devices to store digital data.

The history of data storage doesn't depend on the way in which data is written but actually how it is read - electronically. The first step would be able to read the contents, next to modify the contents (how many times?) and then finally to read the contents again.

No matter DDN managed to provide performance and capacity bundled satisfactorily to a certain extent. They still have to depend on HDDs. Unless we find an alternate storage medium, we would simply be working around the problem and not actually solve it.

So here are few alternatives that are still under-development and will take some company with a lot of money to invest in developments.

Holographic Storage/Nanotechnology:  There are numerous research underway in the field of nanotechnology, some under serious consideration while some in the beginning phase but the good news is there is great possibility of nano tech storage to be the next generation storage.

Blu-ray disc are the commercially available high density portable storage medium. The high density of storage capacity of 50GB (from 700MB of CD and 4.7GB of DVD) was achieved by using a Blue laser that allows information to be stored at a greater density than is possible with the longer-wavelength red laser used for DVDs.
Holographic storage takes to the next level by using two beams to further increase the density. In my opinion, they wont be able to replace Tape as the capacity is currently limited to 300GB. A LTO-4 tape is available with the capacity of 1.6TB. Rewritability is always a concern in optical medium and so is the case with Holographic storage. This could be more of a WORM device than an alternative to existing storage medium.
Further reading: Holographic data storage, Holographic Optical Storage, Quantum Holographic Storage


DNA Storage: I truly believe in this aspiring technology as we all acknowledge that we are pure traces of our ancestors. Now, that much details gets inside minute cells and these cells have so much information to our figures!
Again back to basics, the goal of storage medium is to first read the data - modify it - then be able to read it again. Same is the case with DNA storage. We should be able to read it, Our bio folks have already been able to do analysis and determine ancestral traits of individuals, so the question of reading is fine. Scientists were able to store audio and text on fragments of DNA and then retrieved them with near-perfect fidelity—a technique that eventually may provide a way to handle the overwhelming data of the digital age.

By contrast, DNA—the molecule that contains the genetic instructions for all living things—is stable, durable and dense. Because DNA isn't alive, it could sit passively in a storage device for thousands of years. So the durability is guaranteed (no more failed HDDs so to speak). DNA method can store around 125TB of information in one cubic millimetre of DNA. Now, that's some super-dense capacity to meet future needs. I would be only worried about the seek time(or whatever the term it would be) and the throughput available for reading and writing this large amount of capacity.


The four chemicals in which DNA codes its genetic instructions: adenine (A), guanine (G), cytosine (C) and thymine (T).
Next the zeros are set into either the A or C of the DNA base pairs, and the ones into either the G or T.
Again, even this type of storage is sequential like magnetic tapes. So the seek times could be higher.
Further reading: Future of data storage, Future of data


Overall, none of these technology might not be available overnight and not even in the next 5 years. SSDs in Enterprise took a long while to arrive and they still are outrageously expensive.
Hopefully, technologies like DNA storage should fundamentally change the storage medium to a much faster, reliable, durable and less expensive alternative. These should resolve most of the problems faced today in the storage arena in the data center.

Disclaimer: The views published here are those of the author. They do not necessarily reflect his company's views neither does he gets any benefit from the company owning the products mentioned.