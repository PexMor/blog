---
title: "Bash magic"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["bash"]
tags: ["bash","tricks"]
url: "/topic/bash/"
---

People find the __bash__ a good tool to fulfill miscelaneous tasks. I do also have some nice ones.

<!--more-->

## The exec trick

To redirect stdout+err to file when non-interactive.

```bash
#!/bin/bash

[ -d "$HOME/log" ] || mkdir "$HOME/log"
if [ ! -t 1 ] ; then
    exec >$HOME/log/mylog.log 2>&1
fi
```

## Autocompletion

```bash
openstack complete
```

TO-DO: Unclutter from logging ... [Python Logging](https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/)

## The others

[zsh plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)
