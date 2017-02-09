---
published: false
layout: post
tags:
  - linux
  - tutorial
  - ssh
comments: true
note_class: alert alert-info
note_prefix: Note
note_body: >-
  This post is reference [for a
  class](https://www.digipen.edu/coursecatalog/#CS261) and assumes some prior
  knowledge.
---

This post covers some of the changes to Linux .bashrc file that I have adopted on most of my personal linux servers and virtual machines. The intention is to help improve usability and general productivity when ssh-ing into a linux machine. It was written with Ubuntu/Debian in mind, but should also work on other linux versions such as Mint.

This post assumes you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) or how to [chmod files](https://help.ubuntu.com/community/FilePermissions).

<!-- more -->

<br />


## What is .bashrc?

When using the command line to interact with a linux server, you generally see a default string like `user@hostname:/some/path$`, for example:

![](https://i.imgur.com/oLDm7WQ.png)

That string is constructed by reading from a configuration file, which by default is `~/.bashrc` on most versions of Linux (and `~/.bash_profile` on Mac).

## Enable Colors:

Uncommend the following line:
```bash
force_color_prompt=yes
```

## Add git branch to the Shell Prompt

```bash
# Git branch in terminal prompt.
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ [\1]/'
}
```





## Differentiate Root's color




