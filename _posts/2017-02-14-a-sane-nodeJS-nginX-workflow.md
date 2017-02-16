---
title: A Sane Nodejs Nginx Workflow
published: 'false'
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
note_body: This post is reference [for a class](https://www.digipen.edu/coursecatalog/#CS261)
  and assumes some prior knowledge.
set_id: Linux Server Basics
set_part_id: 3
set_part_title: A sane NodeJS NginX workflow
---

One of the hardest thing when starting out with web backends like [NodeJS](https://nodejs.org/en/) and [NginX](https://www.nginx.com/resources/wiki/) is how to structure your projects. 

You probably want to be able to:

-  Easily test in a VM, and deploy on a Server.
-  Manage configuration files in `/etc/...` and re-use them on multiple machines (local dev VM, production server, etc.)
-  Have old versions of your app up and running on separate URLs without having to change their code.]
-  Have NodeJS handle its own relevant route requests, while NginX handles static files.
-  Remain sane while doing all of this.

<!-- more -->

This post will walk you through some general best practices I have developped within the context of a NodeJS app, using NginX as the HTTP frontend. It is subjective, based off of my own experience with running Linux servers for a few years. Being subjective, the goal of this guide is usability and maintainability.

This post assumes you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) and [linux file permissions](https://help.ubuntu.com/community/FilePermissions), and know how to [run Ubuntu in a VirtualBox](http://www.beopensource.com/2016/05/how-to-install-Ubuntu-1604-LTS-in-Virtual-Box-VmWare.html). Please see the previous posts in this series linked at the top of the post before continuing.


<br />

# Table of Contents

Here is a basic overview of the topics Covered:

0. [Requirements](#requirements)
1. [Using Symbolc Links](#using-symbolic-links)
2. [Linux Directory Structure](#linux-directory-structure)
3. [Setting up NodeJS](#setting-up-nodejs)
4. 

# 0. Requirements

This guide assumes you have glanced at my post on [SSH with Virtualbox](http://blog.johannesmp.com/2017/01/25/port-forwarding-ssh-from-virtualbox/) and have a working Ubuntu Server that you can SSH into using MobaXterm.

On your Ubuntu VM make sure you install the following:

- [NodeJS 6](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04)
- [PM2](https://www.digitalocean.com/community/tutorials/how-to-use-pm2-to-setup-a-node-js-production-environment-on-an-ubuntu-vps)
- [NginX](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)

You only need to get as far as installing them.

([back to Table of Contents](#table-of-contents))

<br />

# 1. Using Symbolic Links

Afundamental tool in the Linux arsenal is the ability to use symbolic links, also commonly called *"Symlinks"*. 

They are used ***everywhere*** in Linux and we will be using them extensively in our Workflow. If you are already intimately familiar with them feel free to continue to the next section.

If you've programmed in C++ you should be familiar with the concept of a reference:

```c++
int    a = 1;
int &r_a = a;  // storing a reference to `a`

printf(a);     // 1
printf(r_a);   // 1

r_a = 5;       // modifying `a` via the reference

printf(a);     // 5
printf(r_a);   // 5
```

A Symbolic link is very similar to a reference. For the user touching the reference, they don't know or care that `r_a` is effectively a pointer and not its own location in memory. They are modifying it as if it was its own int.

## Creating a Symbolic Link

To create a symbolic link we use the `ls` command with the `-s` flag. There are other kinds of links but we will not be covering those here. The basic format is:

```bash
ln -s <path/to/source> <path/to/link>
```

For example, try the following in an empty directory:

```bash
echo "bar" > foo.txt        # `foo.txt` is a text file with contents `bar`
ln -s foo.txt foo_link.txt  # `foo_link.txt` is a symlink to `foo.txt`
ls -la 
```

![](https://i.imgur.com/1DDlU7B.png)

When viewed with `ls -la` valid symbolic links show up as *turqoise*, while broken symbolic links will show up as *red* (assuming you have console colors enabled in your `.bashrc` file):

![](https://i.imgur.com/FZ8XXWZ.png)

If we print the contents of of the file to our  screen with the [`cat` command](http://www.linuxtechi.com/cat-command-examples-for-beginners-in-linux/), we can see that both the original file as well as the symlink:

![](https://i.imgur.com/eH0UkSv.pnghttp://)

Going back to our references example with` foo.txt` and `foo_link.txt`, If you modify the file, either via the original file `foo.txt` or the link, both will show the change. In this regard the symbolic link behaves *exactly* like a reference:

![](https://i.imgur.com/ocqsrFn.png)


## Absolute versus Relative Symbolic Links

When you viewing a symbolic link in `ls -la`, you'll note that the permission string shows an `l` where directories show a `d`. That is because as a link, it can refer to *either* a file *or* a folder, and how it is interpreted depends on which it is pointing at.

So if you link to a directory, it will be interpreted as a directory, and the when viewed with `ls -la` you will see the link:
![](https://i.imgur.com/qTC9ux2.png)

Symbolic links differ from references in how they store the 'reference' to their target.

When you create the link with `ln <path/to/source> </path/to/link>` the reference is defined as the exact ***path/to/source*** you provided.

Symbolic links are evaluated the moment a program attempts to access them, though the OS does cache them so they are actually very efficient. It does mean that if you define the path to your symbolic link in a relative manner that they break.

![](https://i.imgur.com/iZhbd2h.png)

## So Why are they useful? 



([back to Table of Contents](#table-of-contents))

<br />

# 2. Linux Directory Structure

Before we jump into setting up a web app we first need to know about the common directories that we'll need to know about:

## Configs 

**`/etc/`**: Generally every program/service has a folder or config file here. They should be owned by root and modified using commands run with `sudo`.
`/etc`**`/nginx` **: The NginX webserver. Refrain from making edits to the global config `/etc/nginx/nginx.conf` if possible. Anything  specific to your website will belong in one of the `sites-` folders.
`/etc/nginx`**`/sites-available/`**: Configuration files for specific 'virtual servers'. This is where you are intended to store site configurations.
`/etc/nginx`**`/sites-enabled/`**: Symbolic links to config files in `sites-available`. Basically nginx only loads 


([back to Table of Contents](#table-of-contents))

<br />

# 3. Setting up NodeJS

([back to Table of Contents](#table-of-contents))

<br />

# 4. ??
