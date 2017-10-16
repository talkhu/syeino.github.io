---
layout: post
title:  "How to work with different accounts in Git Hub"
categories: Miscellaneous
tags:  git
author: Bo Chen
---

* content
{:toc}

### Cancel the global setting  

    git config --global --unset user.name
    git config --global --unset user.email

### Setup the user.email for each repo  

    git config  user.email "xxxx@xx.com"
    git config  user.name "name"
