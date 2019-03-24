---
title: "JSON and YAML tools"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["tools"]
tags: ["bash","json","yaml"]
url: "/topic/markup/"
---

There are some tools that might help you process, update, write json and yaml files.

## The mother of all those tools

[https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

```bash
# load file as an input into a json (and escape the forbidden characters).
cat index.md | jq -R -s '{v: .}'
```

## The inspiration for yaml (Python make)

This tool is a jq wrapper that let's you work with yaml as if it was json.

[https://yq.readthedocs.io/en/latest](/https://yq.readthedocs.io/en/latest/)

[https://pypi.org/project/yq/](https://pypi.org/project/yq/)

*To install globally or inside [virtualenv](python.html) run:*

```bash
pip install yq
```

The test file: **test.yaml**

```yaml
top:
  subitem: value
  another: "test"
```

Extract the value in yaml format:

```bash
cat test.yaml | yq -y .top.subitem
value
...
```

Extract the value in raw format, in other words *just the value*:

```bash
cat test.yaml | yq -r .top.subitem
value
```

```bash
cat test.yaml | yq -r .top
```

returns a json by default

```json
{
  "subitem": "value",
  "another": "test"
}
```

## The yaml take in golang

[https://github.com/mikefarah/yq](https://github.com/mikefarah/yq)

An amazin' tool written in go, help manipulate the yaml.

*To install:*

```bash
brew install yq
```

```bash
/usr/local/Cellar/yq/2.2.0/bin/yq
/usr/local/bin/yq
```

Dockerish way to run:

```bash
docker run -it -v ${PWD}:/workdir mikefarah/yq sh
```

```bash
/usr/local/Cellar/yq/2.2.0/bin/yq n b.c cat
```

builds

```yaml
b:
  c: cat
```

```bash
/usr/local/Cellar/yq/2.2.0/bin/yq p - cert
```

Combined tools

```bash
# yaml with contents of the stdin
cat index.md | jq -R -s '{v: .}' | /usr/local/Cellar/yq/2.2.0/bin/yq r -

# prefixed but same as above
cat index.md | jq -R -s '{v: .}' | /usr/local/Cellar/yq/2.2.0/bin/yq p - cert

# the plain yq is the python version of yq
cat index.md | jq -R -s '{v: .}' | /usr/local/Cellar/yq/2.2.0/bin/yq p - cert | yq -r .cert.v
```

## YamlLint tool

That helps you to check and verify yaml validity and format the yaml.

[ReadTheDocs: yamllint](https://yamllint.readthedocs.io/en/stable/quickstart.html)

[https://github.com/adrienverge/yamllint](https://github.com/adrienverge/yamllint) - install instructions
