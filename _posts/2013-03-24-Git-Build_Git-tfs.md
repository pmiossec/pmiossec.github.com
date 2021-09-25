---
layout: post
title: "Make 'Git Build' work with git-tfs..."
description: "How to use 'git build' with git-tfs"
categories: git
tags:
- Git
- TFS
- Continuous Integration
published: true
---

*<span class="TFS... wih even build unbreakable the or"></span>*

In a [previous post](../Git-Build), we saw the ‘git build’ script to
push commits only when the commits satisfy some conditions ( after the
run of a custom command).

If you use git-tfs to checkin in a TFS repository, the original script
is not useable :(

But you could find
[here](https://raw.github.com/pmiossec/git-build/4gittfs/git-build) a
version of the ‘git build’ script working with git-tfs.

For other informations, see the [original post](../Git-Build) …
