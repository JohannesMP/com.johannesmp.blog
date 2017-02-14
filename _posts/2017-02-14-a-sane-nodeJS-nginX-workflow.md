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
One of the hardest thing when starting out working with web backends like [NodeJS](https://nodejs.org/en/) and [NginX](https://www.nginx.com/resources/wiki/) is how to structure your projects. 

- You want to be able to easily test and run code on a local VM, but also be able to deploy it quickly on a remote instance. 
- You want to be able to version and manage configuration files in `/etc/...` and re-use them on other machines, while also having the ability of changing behavior (such as favoring debugging on a development server, and favoring speed and reliability on a production server).
- You may want to be able to have old versions of your app up and running on separate directories or subdomains without having to change their code.

This post will walk you through some general concepts and best practices within the context of a NodeJS app, using NginX as the HTTP frontend. It is based off my own experience working with and maintaining a variety of personal Servers and Webapps over the last few years. 

Please Be aware that many of my decisions are highly subjective and may be frowned upon in other contexts (such as larger, more distributed systems). The core focus is to make evrything sane and managable, especially for someone that hasn't used Linux for long.

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


<br />

# 2. Fundamental Tools


<br />

# 3. Setting up NodeJS



<br />

# 4. ??
