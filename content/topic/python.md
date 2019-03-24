---
title: "The Python - System admin right hand"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["python"]
tags: ["OS","python","tricks"]
url: "/topic/python/"
---

* v2 and v3
* pypy
* ipython
* jython
* gunicorn

```bash
# create virtual env
virtualenv -p /usr/local/bin/pypy pypy

# jump in there
cd pypy

# load env
source bin/activate

# run server (with custom code via api object)
bin/pypy bin/gunicorn -b 0.0.0.0:8000 --timeout 30 --workers 32 sampleselect:api
```
