---
published: false
---

In this post I will go over a basic workflow for managing a local Linux server install via [VirtualBox](https://www.virtualbox.org/wiki/VirtualBox) on Windows, with a focus on having a local dev environment that mirrors a dedicated Linux server. This guide will walk you through installing Linux, setting up SSH port forwarding, and include a few useful configuration and workflow tips.

This guide will be using Ubuntu 16.0.1 and [MobaXterm](http://mobaxterm.mobatek.net/) as the SSH client. You are welcome to use another SSH client such as [bash for windows in Windows 10](http://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/) if you so choose.

This post assumes you have used VirtualBox for [running desktop versions of Linux](http://www.beopensource.com/2016/05/how-to-install-Ubuntu-1604-LTS-in-Virtual-Box-VmWare.html), and have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php).

<!-- more -->

<br />
## Downloads 

- Download the latest version of Virtual box: [Download link](https://www.virtualbox.org/wiki/Downloads)
- Download the latest free version of MobaXterm: [Download link](http://mobaxterm.mobatek.net/download.html)
- Download the Latest version of Ubuntu Server, which at the time of this writing is 16.04.1: [Download link](https://www.ubuntu.com/download/server)

Install virtualbox and MobaXterm.

In VirtualBox, click 'New', then Choose Linux and Ubuntu(64-bit)
![](https://i.imgur.com/6imS9c8.png)

Allocate as much memory as you deem necessary. If you are using this virtual machine to mirror a dedicated Linux server, make sure to choose a comparable amount of memory.

Continue setting up a Harddrive. You probably want a dynamically sized Virtual Harddrive with a size limit of at least 20GB.

![](https://i.imgur.com/MUfj6em.png)

The Virtual Machine should now be in VirtualBox's Left Sidebar. Select it and choose 'Start'. When prompted, select the Ubuntu 16.04.1 install ISO that you previously downloaded.

Choose your relevant language, then 'Install Ubuntu Server'

![](https://i.imgur.com/5LFwmqG.png)













