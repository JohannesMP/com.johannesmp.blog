---
published: false
layout: post
tags:
  - windows
  - linux
  - tutorial
  - ssh
  - virtualization
comments: true
note_class: alert alert-info
note_prefix: Note
note_body: >-
  This post is reference [for a
  class](https://www.digipen.edu/coursecatalog/#CS261) and assumes some prior
  knowledge.
set_id: Linux Server Basics
set_part_id: 3
set_part_title: A sane NodeJS NginX workflow
---
One of the hardest thing when starting out with web backends like [NodeJS](https://nodejs.org/en/) and [NginX](https://www.nginx.com/resources/wiki/) is how to structure your projects. 

- You want to be able to easily test in a VM, and deploy on a Server.
- You want to be able to manage configuration files in `/etc/...` and re-use them on other machines.
- You may want to have old versions of your app up and running on separate URLs without having to change their code.
- Lastly, you want to remain sane while doing all of this.

This post will walk you through some general best practices I have developped within the context of a NodeJS app, using NginX as the HTTP frontend. It is subjective, being based off of my own experience with running Linux servers for a few years. As always in programming, there is no 'correct' way and so the focus of this guide is usability and maintainability.

This post assumes you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) and [linux file permissions](https://help.ubuntu.com/community/FilePermissions), and know how to [run Ubuntu in a VirtualBox](http://www.beopensource.com/2016/05/how-to-install-Ubuntu-1604-LTS-in-Virtual-Box-VmWare.html). Please see the previous posts in this series linked at the top of the post before continuing.

<!-- more -->

<br />

# 0. Topics Overview

A is a basic overview of the topics Covered:

1. Linux Directory Overview
2. Fundamental Tools
3. Setting up NodeJS
4. 


<br />

# 1. Linux Directory Overview

before we jump into setting up a web app we first need to know about the common directories that we'll need to know about:



<br />

# 2. Fundamental Tools


<br />

# 3. Setting up NodeJS



<br />

# 4. ??
