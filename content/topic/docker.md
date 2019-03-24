---
title: "Docker the container fenomenon"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["docker","container"]
tags: ["bash","tricks","personal"]
url: "/topic/docker/"
---

* Docker - the original idea, sometimes misunderstood and misused
* Lxd - provided by Ubuntu (VM like containers)
* Rocket or rkt - an alternative
* MessOS or DC/OS - broader framework
* Podman - recent docker migration (```alias docker=podman```)

The following alias helps to organize the docker ps in a neat way.

{% raw  %}
```bash
alias dops='docker ps --format '\''{{json .}}'\'' --no-trunc | jq -r '\''[.Networks,.Names,.Status] | @csv'\'' | tr -d '\''"'\'' | column -s, -t'
```
{% endraw  %}

Another thrick to show mounts of particular docker instance. 

_Save into /usr/local/bin/dofs_

```bash
#!/bin/bash

docker inspect "$1" | jq .[0].HostConfig.Binds
```

### My humble contribution to docker images

> [xlinux/web-incron-demo](https://hub.docker.com/r/xlinux/web-incron-demo)
 - Intended for experiments with incron linked to web page.

> [xlinux/toolbox](https://hub.docker.com/r/xlinux/toolbox)
 - SIPP test tool within an image.

> [xlinux/ansible](https://hub.docker.com/r/xlinux/ansible)
 - Run ansible inside docker. Tested and used for [gitlab runner](https://docs.gitlab.com/runner/).

> [xlinux/gccarm](https://hub.docker.com/r/xlinux/gccarm)
 - A GCC building arm target packed as an image.

> [xlinux/docked-vmware-tools](https://hub.docker.com/r/xlinux/docked-vmware-tools)
 - A base image to install VMWare Tools written in perl.

### Some other interesting repositories

* Sonatype/nexus
* RabbitMQ

