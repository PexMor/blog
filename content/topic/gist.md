---
title: "Gist - git for single file"
author: "Petr Moravek"
date: 2019-03-23T21:25:05+01:00
categories: ["bash"]
tags: ["bash","tricks","gist"]
url: "/topic/gist/"
---

Have your gist at the right place.

<!--more-->

When you want to embed gist from github, you have few options.

This is simply shared link (as provided by gist at the top of gist):

```https://gist.github.com/PexMor/32d457581a3ae8aa1c21d27907e75e7b```

The next option is to select embed the gist by script:

```<script src="https://gist.github.com/PexMor/32d457581a3ae8aa1c21d27907e75e7b.js"></script>```

And the last option which is backed by jekyll-gist plugin:

```{% raw  %}{% gist 32d457581a3ae8aa1c21d27907e75e7b %}{% endraw  %}```

Example of the last options when rendered:

{% gist 32d457581a3ae8aa1c21d27907e75e7b %}
