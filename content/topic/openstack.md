---
title: "Openstack"
author: "Petr Moravek"
date: 2019-03-25T21:25:05+01:00
categories: ["openstack"]
tags: ["SDN","SDS","VM","KVM","python","tricks"]
url: "/topic/openstack/"
---

Openstack the big thing since the cloud inception. Thus here I have collected some material
about real-world problems. With my take on them

<!--more-->

Good news is that cloud actually does not exist :-) It is just a user perception! User does not care about actual server, storage or network. It looks like the servers, storages and networks are obscured by a cloud, but user does not need to know.

## Reverse vision

At first I want to share little bit different vision about virtualization and infrastructure orchestration. Many of those idea generally apply also to new fenomenon of __containers__ (~> _containerization_).

I started to call that __Inside out picture__. Why? Simply because most of the pictures that describe __Openstack__ just starts by enumerating the Openstack components. It is worth to say growing number of componets with variable quality (code transparency, documentation, stability and robustness). Always when I see such list of great components I feel like watching an advertisment for certain product. Do not take it wrong, I do really appreciate the work done for global benefit. I deeply believe that making software open source is the only viable way for general good. But sometimes it looks to me as the user is forgotten and somewhere in the last row of the _theatrum mundi_.

My picture is pretty up-side down. The big center object of the vision is the virtual machine with required resources:

* __VM__ ~ Hypervisor (usually KVM)
	* __RAM__ - allocated from physical machine
	* __CPU__ - slices of raw machine CPU
	* __HDD__ - virtio / block storage (best provided by __CEPH__)
	* __NETWORK__ - virtio / network ring-buffers (DPDK, XDP or SRV-IOV)

__all__ that is then managed by whole lot of various daemons and agents. Recently some of the resources, mainly networking turned into its own spin-off having couple names like SDN, OVN, NFV. Each of them establishing its own purpose right to exists and specific problem they are solving.

Uninformed observer might easily loose his or her focus while looking at the poliferation of the supplementary services providing the elementary __UNIT of COMPUTING__ (aka __UoC__). I was wondering when I spotted __podman__ as a _daemon-less_ container runtime/manager whether such approach could also hit the Openstack domain. All that because some people perceive the openstack as a big beast consuming unnecessary resource just to maintain the IaaS (the very bottom of app stack). There is even a concept of __Container native virtualization__ (aka __CNV__) that take the core of openstack ~ __UoC__ and packs it as a part of the container management platform (as of today __Kubernetes ~ K8S__).

The decoupling of the __VM__ from the __Openstack__ might scare some engineers living in their _home domain_ but what I believe it may make them think different. Of course it might cause _the end of Openstack_ but I do not see as dark as it might suggest. This is the power of opensource that all parts of the software bundle can be taken apart simplified, optimized, replaced, etc and then form a partially new product. Product that can take the best of past and the new.

## Learnign curve 

I have to admit that I have pretty short experience with the Openstack. What exactly I have learned during the common days with it? I have learned that it ressembles a puzzle that has pretty amazing final picture. I brings the fact that you when using it rely on other's people work. As a user or better as an admin of the Openstack instance you have to understand how does the gears fit together. In other words to start an Openstack instance you most likely will encounter __packstack__ or __devstack__ that are out there to let you feel the Openstack. And it is a pretty good teaser! At least I felt in love with it. Sooner or later after teh first encounter you realize that the initial training has passed and you should take it little further. To start earning money out of it. Well at that point you just need some __hardware__ to make the __Virtual hardware__ alive.

## Real-world cluster

Here it comes, growing things bigger needs planning. This is the point when industry practice might come handy. Some external consutancy might be a must. Thankfully there are couple of consulting companies having real world experiences with larger clusters or DCs. A good point to mention here is that when we talk about the Openstack we are silently assuming __on permise deployment__.

I have taken probably the worst and the most painful way to take. My ingredients were rather weak _scholar_ knowledge about the automated deployment using __TripleO__. The TripleO is also behind commercial product _Red Hat Openstack Director_ which gave me the illusion of the right way. Actually I did not have a budget to spend (apart from having some HPE Gen9 servers). I have had some hints from alternative tools. Namely: __MAAS__ + __JuJu__ by __Canonical__ and __Crowbar__ by SuSE. Nevertheless I have taken the integrated solution by Openstack people (should be good when used by authors...).

## Deploy tools

The __TrippleO__ is somehow the ultimate way of doing things. You just throw some config values into it and it does the rest. Well, Uhmm, yes it does. But you will not do it without insight. There are so many variables that only some of their combinations make sense. When ever you create invalid or partially invalid setup you might not get imediate and understandable error code. Or you might even not get any error. Well, just the cluster suddenly falls-over without giving you a chance to be prepared or warned in any way.

The __TripleO__ is amazing collection of __hacked__ tools together. Let me as an example give you list of tools used just for the deploy of overcloud (all of them has to be working properly):

1. Instalation of Undercloud (One node stripped down Openstack) _~ worked on CentOS 7_
2. Deploy of overcloud
	* Turning the enviroments files, heat template templates (.j2) into final heat templates (~__HOT__) to define the __HW__ nodes with their configs
	* Executing the __HOT__ at the undercloud, as a part of that the __HW__ nodes are deployed with base __OS__ (Centos 7) ~ __Openstack - Ironic__
	* Downloading the __Ansible playbooks__ that will preprare the __HW nodes__ with necessary packages and configs
	* Execution of those __playbooks__ via __SSH__
	* Starting the Openstack services (components) inside containers (docker) at the individual nodes. This step is actually done in 5 phases using mainly __Ansible__ but also __Openstack - Kolla__ using __puppet__

And in between those steps and huge number of scripts. Abolutely anywhere an error might occure. I can imagine that most of the errors remain unspotted and causing harm later in time. Why? Simply because if there is a multiple sources of code. Each and every programmer has some model od reality in his mind. The code and its purpose and authors intention/expectation will deviate from yours. Only few cases can be covered by input value tests. Thus having a tool of __TripleO__ is great if its magic matches with your configs and intentions but on its own does not give any warranty. You are pretty much left on your own with pray and hope.

As easy it was to fell in love with such a magic tool so hard it is to leave the idea that it could work as an almighty solution doing the magic of uprising the Openstack on crappy collection of __HW__.

So far I felt like I have got trapped by one of the obvious fallacies _"Because I and they have invested so much into it it must be good and right solution!"_

To get out of that pitfall I am currently persuading myself to divide and conquer next step. One part is to have __devstack__ for experiments and the second part is to turn to alernative that has more __atomized__ deployment process as there are so many aspects to consider.

Some of them are:

* Performant __Block storage__ provider - __CEPH__ as best candidate
* __High Availability__ and Redundancy - each components in production shall have some reliability and upgrade path
* __Self Healing__ - automatic rollback of failed components
* __Autoscaling__ - on demand cluster of VM resize (heavily depends on the app)
* __High Perfomance Networking__ - all scale from __Phy__, __SRV-IOV__, __DPDK__, __XDP__, __VxLAN__, __VLAN__, __eBGP VPN__ 
