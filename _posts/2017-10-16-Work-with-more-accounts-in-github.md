---
layout: post
title:  "Work with more accounts in Github"
categories: Miscellaneous
tags:  git
author: Bo Chen
---

* content
{:toc}

# Cancel the global setting

```bash
    git config --global --unset user.name
    git config --global --unset user.email
```

# Setup the user.email for each repo

```bash
    git config  user.email "xxxx@xx.com"
    git config  user.name "name"
```