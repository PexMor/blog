---
title: "Ansible no.4: Operator SDK"
author: Petr Moravek
date: 2019-04-17T09:25:05+01:00
categories: ["prgcont"]
tags: ["k8s","operatorsdk","ansible"]
---

The recent meetup held at MSD on Monday 2019/04/15 brought elementary insight into the ansible as an operator and in particular ansible [K8s](https://docs.ansible.com/ansible/latest/modules/k8s_module.html) module.

The story has begun with reference requirement to deploy [NextCloud](https://nextcloud.com/install/) which a rebirth of NAS software for Linux.

The first step was apply Ansible role [nextcloud](https://github.com/rbicker/ansible-nextcloud) to an existing server. The server could be acquired in the cloud at AWS, DigitalOcean or at own permises via virsh, Vagrant/virsh, MaaS, Openstack whichever applies to your setup.

> __Big Fat Warning:__ Software downloaded from internet might contain bugs, features, malware, ransomware and other _goodies_ of today. Do not trust anything you download without own check, scans or review. Please take care of your safety and security!

And as we are K-native aka geeks willing to take any cloud as long as it is based on Kubernetes then the straight next step was let us do it via operator and on K8s.

The original option has quite a few potential flaws like:

- no backup
- no scaling
- no upgrade path
- no rollback
- no layers

Those concerns should be pretty much handled by any orchestration, automation and monitoring. So we are container-minded thus we follow that path.

For all necessary steps please have look at [README](https://github.com/prgcont/workshop-k8s/blob/master/07_ansible_operator/README.org) in the meetup folder. 

The principle is pretty simple. We use ```CRDs``` to extend the K8s which in turn gets processed (consumed) by the ansible operator. This operator takes the ```CRD``` yaml and makes an ansible value tree out of it. These values are passed into the __ansible playbook__. The playbook utilizes ansible templates to make K8s definitions of required resources (deployment, service and ingress). These resources are fed back to the K8s to setup and run necessary containers and respective routing.

That is in short all the __magic__. During the session the original and simple __NextCloud__ was scalled little bit up by adding __MySQL__ option instead of __SQLite__.

> Note: for the simplicity the __Persistent Volume Claims__ and __Persitent Volumes__ for the actual __NextCloud__ storage were completely omitted. 

Jakub has also mentioned that he was thinking of intermediate step which should have done the start of bare OS container and installing __NextCloud__ using the original ansible role. Unfortunately the use of __ssh__ into k8s was not viable.

> _Could it be even done, could other mean of attaching to container used like_ __exec__?

And the last thing to mention during the workshop also topic of re-using the existing playbooks surfaced (which Jakub has tried to unsuccesfully exploit). 

There was also a short note on __imutable infrastructure__ vs. __flexible deployments__. The issue of imutable infrastructure is that every change requires new __snapshot__ of the installed software and there is a little room for modifications and/or __debugging__. While __flexible deployment__ taken to the extreme means that you start with bare OS image and configure and setup everything upon every start of the container. The second approach sounds like VMish way and also could remarkably slowdown the bootstrap of running app (time to use/login). Therefore each and everybody be warned, there is no Holly Grail and as many consultants say __It depends__ (__before issuing invoice for consulting services__).

Some past K8s operator related talks or workshops:

- [```06_operator_sdk```](https://github.com/prgcont/workshop-k8s/tree/master/06_operator_sdk) - the into (golang)
- [```07_ansible_operator```](https://github.com/prgcont/workshop-k8s/tree/master/07_ansible_operator) - this talk in code
- [```pycont-demo```](https://github.com/prgcont/pycont-demo) - Python way
