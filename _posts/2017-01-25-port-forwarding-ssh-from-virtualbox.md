---
layout: post
title: 'Port Forwarding SSH from Virtual Box'
tags:
  - windows
  - linux
  - tutorial
  - ssh
  - virtualization
published: true
comments: true
note_class: "alert alert-info"
note_prefix: "Note"
note_body: "This post is for a [class that I TA](https://www.digipen.edu/coursecatalog/#CS261) and assumes some prior knowledge."
---

In this post I will discuss setting up port forwarding to manage a local linux virtual machine through ssh, with a focus on mirroring the workflow you might follow with a dedicated Linux server.

This guide will be using Ubuntu 16.04.1 and [MobaXterm](http://mobaxterm.mobatek.net/) as the SSH client. You are welcome to use another SSH client such as [bash for windows in Windows 10](http://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/) if you so choose.

This post assumes you have used VirtualBox for [running desktop versions of Linux](http://www.beopensource.com/2016/05/how-to-install-Ubuntu-1604-LTS-in-Virtual-Box-VmWare.html), and have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php).

<!-- more -->

<br />

## Downloads 

- Download the latest version of Virtual box: [Download link](https://www.virtualbox.org/wiki/Downloads)
- Download the latest free version of MobaXterm: [Download link](http://mobaxterm.mobatek.net/download.html)
- Download the Latest version of Ubuntu Server, which at the time of this writing is 16.04.1: [Download link](https://www.ubuntu.com/download/server)

<br />

## Installation

Install VirtualBox and MobaXterm.

Go through the general VirtualBox install for ubuntu. Keep in mind:

- Choose a Dynamically sized Virtual Harddrive of a reasonable size, probably limited to 20GB or so (can always be changed later)
- When starting for the first time, select your downloaded ISO when prompted.
- Most settings should be left default.
- When prompted to choose `Software to install`, make sure to install OpenSSH Server (miove the cursor down and press space to select it, **NOT** enter.)
https://i.imgur.com/9mdAgY5.png
  - If you accidentally skip installing the OpenSSH Server you can always install it later with `sudo apt-get install openssh-server`


<br />

## Initial Setup

Once the install has finished, you will be prompted with the standard Linux login.

![](https://i.imgur.com/r5DirQg.png)


While you can log in here and use this console to interact with your server, it is very limited:

- You cannot resize the window.
- You cannot copy/paste text from your host OS.
- You cannot scroll to look at more history than is currently on the screen.

Luckily we will only log into it this way once before we can use a proper SSH client.

Log into the account you created during installation. Once logged in run the following command to see if SSH is running: `sudo service ssh status`. If you see something like this:
![](https://i.imgur.com/klsCaAB.png)

Then you have ssh properly installed.

If you see an error then you may need to install ssh with `sudo apt-get install openssh-server`

Once you see that SSH is running we will need the local IP address that VirtualBox is using internall for this Virtual Machine. To do so we can run ifconfig and filter it for lines that contain the string "inet addr". Run `ifconfig | grp "inet addr"`
![](https://i.imgur.com/QNw8eVl.png)

127.0.0.1 is your loopback addtress so In this case the IP address you want is **`10.0.2.15`** but that may be different in your case. Make a note of this IP address, so we can use it to port forward SSH connections from your host OS into the virtual machine.

You can now type `exit` to log out of your user session. Ideally we will never have to log in through this console again, and will instead use SSH.


<br />

## Port forwarding SSH

Minimize your server's window (make sure not to close it), select it in the VirtualBox Siderbar and choose '_Settings_'.

Select the '_Network_' tab and expand the '_Advanced_' section:
![](https://i.imgur.com/C93FvOI.png)

We will now forward an unused port from our native host OS to the SSH port (port `22`) on the virtual machine. To do so we will have to pick a port that is not already in use. In this case we will use port `2222`.

Click '_Port Forwarding_', and create a new Rule as follows (make sure to change `10.0.2.15` to match the IP address you noted down earlier):
![](https://i.imgur.com/KZj31Jh.png)

Basically this rule allows any requests made on port 2222 of your local machine to be redirected to port 22 of the virtual machine. 

Click 'OK' to close the settings window and open your SSH client of choice, such as MobaXterm.


<br />

## Connecting via SSH

Start up MobaXterm:
![](https://i.imgur.com/Ryl1HxW.png)

In the main section click 'Start local terminal', which will start up a standard bash shell:
![](https://i.imgur.com/A4M1Z9f.png)

You should now be able to connect to your Virtual Machine via this command: `ssh -p 2222 USER@127.0.0.1` where `user` is the name of the user you created:

![](https://i.imgur.com/diQZkjc.png)


<br />

## More port forwarding

You can forward any port from inside your VM this way. For example, if you running nginx as a webhost on port 80, you can forward it to your host OS to view the page it is hosting. Since port 80 would already be used by your main OS, you would use another unused port, such as port `8080`:

![](https://i.imgur.com/wBKzbU1.png)

Which you can then view in your host OS' browser as follows:

![](https://i.imgur.com/2UA0T95.png)












