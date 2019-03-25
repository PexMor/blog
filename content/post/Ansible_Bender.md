---
title: "Ansible no.3: ansible-bender"
author: Petr Moravek
date: 2019-03-21T22:25:05+01:00
categories: ["prgcont"]
tags: ["buildah","podman","ansible"]
---

This time we have come together to talk about containers or their images managed by __ansible__.
We have spoken about __buildah__ and __podman__ in concert with above mentioned system management tool.

<!--more-->

## Initial rehearsal

Actually before you begin you are expected to be familiar with some buzz-words. Let us try to associate their meaning with the context throughout the session.

### Ansible

Our old friend thus not need for further details [https://www.ansible.com/](https://www.ansible.com/) and [https://github.com/ansible/ansible](https://github.com/ansible/ansible).

### Podman 

> __Podman__ is a fully-featured container engine. It provides a Docker-compatible command line to ease the transition from other container engines. Most Podman commands can be run as a regular user, without requiring additional privileges. Furthermore, all of this is accomplished __without a daemon__!
> -- <cite>Podman 1.0 release</cite>

Well it is worth to say that podman is written in __golang__ the magic ingredient of today’s fancy and successful projects. It comes from Red Hat and but is has been released into public domain (Apache-2.0 license, Github: [tutorial/install](https://github.com/containers/libpod/blob/master/docs/tutorials/podman_tutorial.md)). 

> _Our original goal with Podman was to provide a fully-featured debugging experience for CRI-O, but it has become so much more._
> 

__Note-podman:__ Ubuntu users ~ at 16.04LTS you are out of luck :-( Debian, Ubuntu 18.04? Fedora/RHEL family welcome. _Do you really want me to build from source and enjoy the hell of dependencies?_

### Buildah

__Buildah__ is an efficient way to create OCI images. It has a home at Github (Github:[Containers/buildah](https://github.com/containers/buildah)). This is the second piece of puzzle. It is again made with golang. Again born in Red Hat ([A daemon-haunted (container) world no longer: ...](https://www.redhat.com/en/blog/daemon-haunted-container-world-no-longer-introducing-buildah-10)).

Those two pieces has established a new wave of container management tools that are trying to get rid of old mistakes and undesired vulnerabilities. With all that in mind we can say: "The __$king__ is dead, long live the __$king__!” Where let the variables be: Docker and Podman+Buildah (at this moment ;-)).

__Note-buildah:__ Even Ubuntu 16.04 (Xenial) can build images. But cannot run containers as it lacks CRI-O by default. But can make it by PPA :-)

```
sudo add-apt-repository ppa:projectatomic/ppa
sudo apt-get update
```

## The session

__Dockerfile is wrong ! However...__

(brought by [Tomáš Tomeček](https://github.com/TomasTomecek))

### Small talk on [Ansible-bender](https://github.com/ansible-community/ansible-bender)

([slides](https://tomastomecek.github.io/speaks/2019-prgcont-ansible-bender/) / their [source](https://github.com/TomasTomecek/speaks/tree/master/2019-prgcont-ansible-bender))

#### Buildah

* Dockerfile reborn (```buildah bud .``` saves your life)
* From scratch (of course be minimalist)
* Bash copy (container image mounted)

then marry it with __ansible__ though ~ __ansible_bender__

* interface between ansible and buildah (stdin/out/err)
it is __just__ another ```-c``` buildah like ssh or local
* you decide which layers are cached (up to apt upgrade or install ~ slow parts fast)
* turn ansible playbook from __SSH+VM__ to __buildah-inside-container__
(without modifying the playbook!!!)
* Docker take on the same topic: docker build -> Buildkit (after few years of stagnation)

__plans__ ~ future is bright

kNative ~ k8s integration (build part by ansible-bender)

#### Hands-on warm-up

Let's start in shell

```bash
export BASE_IMAGE=fedora:29
export CONT_NAME=prgcont
```

buildah unshare ‘...’ for unprivileged user - still buggy, so become root for now...

```bash
buildah pull $BASE_IMAGE
buildah from --name $CONT_NAME $BASE_IMAGE
printf "$CONT_NAME \
  ansible_connection=buildah \
  ansible_python_interpreter=/usr/bin/python3" >inventory-container
ansible-playbook -i inventory-container -c buildah ./recipe.yaml
buildah commit --cmd '/entrypoint.sh' $CONT_NAME my-fancy-image
buildah rm $(CONT_NAME)
```

push it among local docker images:

```
buildah push prgcont docker-daemon:prgcont:some-name
```

#### Hands on second part

[Sources](https://github.com/prgcont/workshop-ansible-containers)


We'll do two common use cases in this workshop:

1. Containerizing a test suite. (done)
2. Running a web app in a container. (done)

```bash 
ansible-bender push docker-daemon:hugo:latest
```

__Tip:__ use __sen__ (Github:[TomasTomecek/sen](https://github.com/TomasTomecek/sen)) - a terminal UI for docker

## QA Part

__Question:__ How does the ansible interface with the buildah?

__Answer:__ stdin/out/err, but the cousin project __podman__ uses structured API  called varlink ([see FAQ of varlink](https://varlink.org/FAQ)).

## One more thing

* [Quarkus](https://quarkus.io/) - Red Hat's take on Java Fast(TM)
* [Drools](https://www.drools.org/) (BPML) - slow java -> graalvm (AOT) made fast (from Red Hat blog [Quarking Drools...](https://developers.redhat.com/blog/2019/03/14/quarking-drools-how-we-turned-a-13-year-old-java-project-into-a-first-class-serverless-component/))
* [OptaPlanner](https://www.optaplanner.org/) - a path optimizer (path for your VMs towards right hypervisor)

## Off-topic

__Question:__ How to replace legacy of __Mirantis Openstack__ with something fresh?

__Answer:__ Well there is a couple of other projects doing the same thing (deploy and manage Openstack). The well known are __TripleO__ and its commercial cousin __Redhat Director__ but what about to consider shrinking the problem into __just__ making the __VM__ behave like a __container__? Then it is worth to check the idea behind Container Native Virtualization (aka CNV see [as preseneted on Red Hat Summit - May 2018](https://www.redhat.com/files/summit/session-assets/2018/Introducing-Container-native-Virtualization.pdf)).