---
title: "Certmonger the Certificate Authority"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["certificates"]
tags: ["bash","tricks","cert","FreeIPA"]
url: "/topic/cert/"
---

Have you ever wondered how to make certificate __the right way__? Certmonger is worth checking.

<!--more-->

* IPA
* Local
* Third party?

### Pre-requisite package

```bash
sudo -H apt install certmonger
```

### Request the certificate

```bash
sudo -H getcert request \
	-c local \
	-f dummy.crt \
	-k dummy.key \
	-N CN=dummy.test.lab \
	-D dummy.test.lab \
	-U id-kp-serverAuth
```

### List available CAs

```bash
sudo -H getcert list-cas

CA 'SelfSign':
	is-default: no
	ca-type: INTERNAL:SELF
	next-serial-number: 02
CA 'IPA':
	is-default: no
	ca-type: EXTERNAL
	helper-location: /usr/lib/certmonger/ipa-submit
CA 'certmaster':
	is-default: no
	ca-type: EXTERNAL
	helper-location: /usr/lib/certmonger/certmaster-submit
CA 'dogtag-ipa-renew-agent':
	is-default: no
	ca-type: EXTERNAL
	helper-location: /usr/lib/certmonger/dogtag-ipa-renew-agent-submit
CA 'local':
	is-default: no
	ca-type: EXTERNAL
	helper-location: /usr/lib/certmonger/local-submit
```

### List monitored certificates

```bash
sudo -H getcert list

Number of certificates and requests being tracked: 1.
Request ID '20181214222908':
	status: MONITORING
	stuck: no
	key pair storage: type=FILE,location='/root/dummy.key'
	certificate: type=FILE,location='/root/dummy.crt'
	CA: local
	issuer: CN=56a3a6a7-13aa4362-841c6f8f-b4588a25,CN=Local Signing Authority
	subject: CN=dummy.test.lab
	expires: 2019-12-14 22:21:27 UTC
	dns: dummy.test.lab
	eku: id-kp-serverAuth
	pre-save command:
	post-save command:
	track: yes
	auto-renew: yes
```
