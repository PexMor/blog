---
title: "Podman playground VM"
author: Petr Moravek
date: 2019-09-01T11:25:05+02:00
categories: ["prgcont","containers","kvm","vagrant"]
tags: ["buildah","podman","skopeo"]
---

To take some summer inspiration, I have decided to check the ```podman``` family on my __Ubuntu 16.04__ machine. Moreover, it was not that easy as it first looked.

<!--more-->

## Fail fast

The ```podman``` family (the other siblings are ```buildah``` and ```skopeo```) has its repo and install guide at
[https://github.com/containers/libpod/blob/master/install.md](https://github.com/containers/libpod/blob/master/install.md).
However, trying that on my ageing __Ubuntu 16.04__ distro was nothing you might like to exercise. In a few words, an attempt to install the packages led to the failure of other packages. The ultimate final step was to remove all that which fortunately fixed the world.

Yes, my computer is my __pet__, and I wanna maintain that state.

Yes, I do want to make distro update :-).

## Second thought

Ok, back to point zero. What is the next option?

* All right, the containers do not pose many requirements of the layers below
* Decently up to date Linux kernel is OK

So what about a virtual machine? I want to have it portable and eventually hosted. Then some standard __stock VM image__. A __cloud image__ of some distro. Well, to reduce the number of possible distros the is simple guidance: take what it creators use. The company behind is the Red Hat. There are at the moment few options __RHEL7__, __RHEL8__ those require at least developer license. Eventually, I could turn to __Centos7__ or __Fedora__ line. There is a clear trade-off with the __Centos7__ is close to end-of-life besides that its future is unclear. Considering all that, the only option that remains is __Fedora__ and at the time of writing its release 30.

So I have the cloud image [https://alt.fedoraproject.org/cloud/](https://alt.fedoraproject.org/cloud/).

The adjective __cloud__ suggests that it should run pretty well in any VM fabric. Further, it even has the __Amazon AMI__ ready. However, let me start small then grow. The initial exercise would be my elderly __Ubuntu 16.04__. To expedite the bootstrap, I wanted to use the __Vagrantfile__ based. The Vagrantfile brings the great all in one and infrastructure as a code to your desktop.

__Nothing is perfect :-(__ Vagrant is powered by ruby, but the __Ubuntu 16.04__ does not have the decent version by default. To release me from the circular dependencies (RVM also considered), I did the same already couple times. Run the tricky part in the separation and separation in its tinnies and robust forms means container. That brought me to the point of having vagrant running in a container. __Docker container__ if you are asking. 

__Wait!__ The container is meant to prevent from accessing the hardware. Yes, you are right! However, as I want to use the __libvirt/KVM__ provider, there is a way of doing it. The libvirt daemon communicates with the client via socket (or even ssh pipe). That socket can be mapped into the container!

So now I have all the ingredients ready:

* Vagrant in docker (with vagrant-libvirt plugin)
* Cloud image of Fedora 30
* libvirt working on the host with Intel VT-X

Let me connect those by the Vagrantfile.

I have put those ingredients to the repo at Github [https://github.com/PexMor/fedora-container-in-vm](https://github.com/PexMor/fedora-container-in-vm).

All the rest are just two simple commands: ```vagrant up``` and ```vagrant ssh``` and there you are, command line with ```podman```, ```buildah``` and ```skopeo``` at your service.

## Ubuntu side note

IaaS in a box (Ubuntu), running VM in the course of minutes. My past great experience with uvtools and uvt-simplestreams-libvirt.

```
sudo apt -y install uvtool
uvt-simplestreams-libvirt sync release=bionic arch=amd64
uvt-kvm create secondtest release=bionic
uvt-kvm ssh secondtest --insecure
```
