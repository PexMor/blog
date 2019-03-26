---
title: "TripleO Undercloud"
author: "Petr Moravek"
date: 2019-03-26T21:25:05+01:00
categories: ["openstack"]
tags: ["tripleo"]
url: "/topic/undercloud/"
---

Have you ever had a broken TripleO Overcloud on PXE servers with no LAN connection for some reason. Then you might find these hints useful.

<!--more-->

Having broken Overcloud is eqvivalent to DC outage. In production it means you are loosing money. In lab case it means that you cannot continue tests.

Assuming your servers has some __IPMI__ and __BMC__ on board, most servers today do. The names might vary: iLO (HPE), iDrac(Dell), BMC(IBM/Lenovo), iRMC(Fujitsu), IPMI(SuperMicro), IPMI via UCS(Cisco)

You have had used the IPMI to control your server's power state and selecting the server boot path (LAN, iSCSI, ...). Apart from that there is also an option for __SoL__ aka Serial Over LAN.

With just __ipmitool__ installed you can check this great feature as follows:

```bash
ipmitool -I lanplus -U USER -P PASSWORD -H IP.ADDRESS.OF.IPMI sol activate
```

> Note: To find the appropriate package on Debian family: ```apt-file search ipmitool``` and on Red Hat family: ```rpm -q --whatprovides /usr/bin/ipmitool```

When you have that access you are gaining the __virtual /dev/ttyS?__ in linux. In my case that made it little tricky, the port was __/dev/ttyS1__ which is __COM2:__ in DOS/Win world (Depends on machine config, but by default it is COM2:). Why the heck I need access to serial port? Good question! __A:__ for the case when your machine loses the network connectivity. Wait! You just said it is over LAN. Yeah, well it is but __out of band__ it means via the BMC/IPMI/Management LAN, which should never break! :-) Great! But will that just work out of the box? Well, __NOPE__! You have to make small homework. But it pays back then. If you wish __Ansible__ is a good friend.

```bash
# to turn on the serial console on /dev/ttS1 (COM2)
systemctl start serial-getty@ttyS1

# to enable it for next boot
systemctl enable serial-getty@ttyS1
```

as __Ansible__ one-liner:

```bash
ansible -i "target-host," -m systemd -a "name=serial-getty@ttyS1 state=started enabled=yes"
```
