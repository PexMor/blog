---
title: "Linux kernel based virtual machines"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["virtualization"]
tags: ["bash","tricks","kvm","kickstart","veth"]
url: "/topic/kvm/"
---

Kernel virtual machine, The Linux's take on hypervisor thing type 2 (after main OS, but turning itself into type 1).

<!--more-->

Hypervisor runs apps on bare metal as long as it has no external communication.
[Alfred Bratteurd talk on CPP-2016 about Include OS](https://www.youtube.com/watch?v=t4etEwG2_LY) - Unikernel.

* js linux
* tap
* veth
* brctl
* kickstart
* dhcp
