---
published: true
layout: post
tags:
  - linux
  - tutorial
  - ssh
comments: true
set_id: Linux Server Basics
set_part_id: 2
set_part_title: SSH Workflow Tips
note_class: alert alert-info
note_prefix: Note
note_body: >-
  This post is reference [for a
  class](https://www.digipen.edu/coursecatalog/#CS261) and assumes some prior
  knowledge.
---
Here is a small collections of tips and tricks when working with a linux server. The intention is to help improve usability and general productivity when ssh-ing into a linux machine. It was written with Ubuntu/Debian in mind, but should also work on other linux versions such as Mint.

A quick Overview:

1. Use _**history**_ and _**grep**_
2. Use the command line faster
3. Use _**nano**_ for config files
4. Some useful applications
5. Customize your _**.bashrc**_ file

This post assumes you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) and [linux file permissions](https://help.ubuntu.com/community/FilePermissions).

For the most part these are all fairly straightforward and this post will probably contain nothing new for anyone that has a fair amount of linux experience.

<!-- more -->

<br />


## 1. Use _**history**_ and _**grep**_

Often you may want to look up a command you ran earlier, or even days ago when you were installing something and you want to know exactly what you did (for example if you want to repeat the same steps on another server or VM).

If you type `history` you will get a list of all commands you've typed recenctly, including past sessions.

You can pipe that output into the `grep` command to filter out all the commands that had a specific string. So for example if you want to find every command you've ever run containing "test", you can do `history | grep "test"`:

![](https://i.imgur.com/KzuJY0H.png)

Note that each user has their own history, so if you use a command with `sudo`, such as `sudo apt-get install ...` the command will only show up in the `root` user's history. 

Running `sudo history` may result in a `sudo: history: command not found` error. To get around this you'll need to elevate yourself to be the root user first:

````bash
sudo su                           # You are now root
history | grep "apt-get install"  # Running history as root
````

<br />


## 2. Use the command line faster

Some basic, but useful tips to make using the command line far less tedious:

### Use _**Tab**_ to autocomplete

When you are navigating a complex folder structur, you should always press tab after typing the start of the path you want to travel. 

For example, if you want to navigate to:

````bash
~/ReallyReally/VeryLong/AwesomePath
````
You can do so easily by typing `cd ~/R` _**Tab**_ `V` _**Tab**_ `A` _**Tab**_:
![](http://i.imgur.com/ex1NMoE.gif)

Assuming that at each level the name of the directory is unique. Each time you hit path, the console would attempt to auto-complete as much of the path as possible.

If you had both `~/ReallyReally` and `~/ReallySorta` then typing `~/R` _**Tab**_ would autocomplete to where the two names differ, at which point typing either `R` _**Tab**_ or `S` _**Tab**_ would complete that directory name. When it stops you can hit tab again to see all possible options.

This is different from the behavior of the Windows commandline, which auto-completes the first option, and each tab cycles to the next option.

### Use _**!!**_, _**!$**_ and _**!!:n**_ to quickly repeat commands

The `!!` macro is replaced by your last command, allowing you to quickly re-run commands with `sudo`:
![](https://i.imgur.com/uWofC8b.png)

Similarly you can grab only the arguments to the last command with `!$`, so you could use that to re-run a command that had `sudo`, without the `sudo`.

Lastly, if you ever want to grab a specific argument from the last command, you can use `!!:n` to grab the n'th argument:

![](https://i.imgur.com/casgnqE.png)


### Use _**Ctrl**_+_**R**_ to auto-complete past commands

As in the windows commandline, you can press the up and down buttons to navigate your most recently run commands.

However if you type `Ctrl`+`R` you can get an auto-completing `reverse-i-search`. Simply start typing and the best matching command will fill in, then press enter when you have selected the right command. 

Repeatedly hitting `Ctrl`+`R` will cycle through all commands that might match, even with fuzzy searching:
![](http://i.imgur.com/M0fcqEB.gif)


<br />

## 3. Use _**nano**_ for config files

`nano` is probably the easiest linux text editor to get into, and ideal for small config changes. It's what I'll be using in the rest of this tutorial, and there are some small changes you can make to make it more usable.

While learning more full-featured editors such as [vi](https://www.cs.colostate.edu/helpdocs/vi.html) is encouraged if you plan to do serious development on linux servers, nano is really easy to get a hang of. 

### Basic _**nano**_ Commands

  - Creating/Opening a file: `nano <filename>`
  - The standard UI:
    https://i.imgur.com/Tvt9Hm4.png
      - The commands are listed at the bottom, and all are of the format `Ctrl`+`Letter` (`^` stands for `Ctrl`)
  - `Ctrl`+`O` to save document.
  - `Ctrl`+`X` to close document.
    - If pending changes enter `y` and enter, and when prompted for filename hit enter again.
  - `Ctrl`+`W` to search for a specific string in the document,
  - `Ctrl`+`K` to 'cut' the current selection (default whole line) Use `Ctrl+U` to 'uncut' (copy and paste)
  - `Ctrl`+`-` or `Ctrl`+`_` to jump the cursor to a given line number.

_**IMPORTANT**: `Ctrl`+`Z` will minimize an open document **without actually closing it**. This is an easy mistake to make when first starting to use nano. To bring it back to the foreground use the command `fg`_


### Some _**nano**_ Configuration changes

Some small changes can be made to the default nano configuration found in `/etc/nano/`

`sudo nano /etc/nanorc`

(Remember, you can use `Ctrl+W` to search for them)

  - `set constantshow` : you always see the current line number in the bottom bar.
  - `set mouse` : can use mouse for some things, like setting the cursor's position.
  - `set softwrap` : if you want to see long lines in one screen
  - `set tabsize 8` : uncomment and change to whatever value you prefer.

<br />


## 4. Some useful applications

### Use _**htop**_ to view running processes

`htop` is a fantastic interactive process and system resource viewer. You can view all currently running processes, as well as system Memory Usage, CPU usage, Load Average and system uptime.

Simply install it with:

````bash
apt-get install htop
````

And run it by typing `htop`, and close it with `F10` or the standard `Ctrl`+`C`.

![](https://i.imgur.com/8PDWqXa.png)

Some useful commands:
- `F6` you can choose what column your processes are sorted by, allowing you to easily identify CPU or Memory hogs:![](https://i.imgur.com/gxptGzN.png)
- `F5` to toggle between sorted mode and tree mode (where child processes are stacked below parent processes)
- Move the cursor up and down with the arrow keys and hit `F9` to send a signal, such as `Sigterm` to kill a process.
- `F4`, type in a name to filter and hit enter to only display certain processes
- `F2` and use the arrow keys to navigate the configuration settings.


_**Note:** Certain processes will only be visible to root, and can only be killed by root, so when necessary run `sudo htop` instead._

### Use _**supervisorctl**_ to easily daemonize _anything_ 

If you ever want to daemonize a script or application so that it runs without supervision, this can be a pain without specialized applications such as `pm2` for `node.js` applications.

Enter [supervisor](http://supervisord.org/), a powerful solution for managing and running processes without having to resort to writing startup scripts or using screen.

It is far too large to cover here, but it's so painless and useful compared to really any alternative that it's worth mentioning. I personally use it for running several game servers that don't provide straightforward daemonization.

Here is a good tutorial to get you started if you want to use it: https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps

_**Note**: for node.js applications [PM2](http://pm2.keymetrics.io/) should be used instead_



### Use _**TheF*ck**_ when frustrated

We've all been here:

![](https://i.imgur.com/P0k81SK.png)

 > _AGH!! Yes just do it as root already, you know what I meant!_

While you could use the `!!` macro  to simply re-run the command, you can instead install [TheFuck](https://github.com/nvbn/thefuck) to simply vent your frustration, and fix your mistake all at the same time:

![](https://i.imgur.com/HuOF3PG.png)

It will intelligently try to auto-complete the most recent command you typed. It works for a ton of other commands as well.

It is a python script that you can install via [pip](https://pypi.python.org/pypi/pip) like this:

````bash
sudo apt update
sudo apt install python3-dev python3-pip
sudo -H pip3 install thefuck
````

And then you will need to edit the `.bashrc` startup file located in your user's home directory:

````bash
nano ~/.bashrc
````

Navigate to the bottom and add the following:

````bash
# Default TheFuck Alias (type 'fuck' to activate)
eval "$(thefuck --alias)"
# Custom TheFuck Alias (type 'FUCK' to activate)
eval "$(thefuck --alias FUCK)" 
````

And then re-load the startup file, either by disconnecting and reconnecting, or by running

````bash
source ~/.bashrc
````

<br />


## 5. Customize your _**.bashrc**_ file

Speaking of `.bashrc`, it is a startup script located in a user's home directory that is run when they log in. It sets up settings, such as what the part of their console to the left of the `$` looks like.

Any change you make to a `.bashrc` file will only take effect when it is reloaded, either by relogging or typing `source path/to/file`.

_**NOTE:** each user has their own `.bashrc` file. For example root's is located in `/root/.bashrc` instead of `/home/<username>/.bashrc`. It can be useful to temporarilly switch to root via `sudo su` and so changes you make to your `~/.bashrc` file should also be applied to `/root/.bashrc`._


### Add custom command aliases

You can define custom command aliases easily with the format `alias <name>='<command>'`. 

For example, if you are used to using `dir` in the windows commandline and can't stop accidentally typing that instead of `ls -la`, you can add this alias:
````bash
alias dir='ls -la'
````

Instead of typing `sudo apt-get update` and `sudo apt-get upgrade` you could define just `update` and `upgrade`:
````bash
alias update='sudo apt-get update'
alias upgrade='sudo apt-get upgrade'
````

### Change your _**PATH**_

If you plan on installing custom commands and want to make sure they appear in your path, `.bashrc` is where you do so.

Let's say you want to add `/home/username/testing` to your path. You would include the following line at the bottom of your `.bashrc` file (on linux the PATH variable is delimited with colon `:` characters))

````bash
export PATH=$PATH:/home/username/testing
````

Which would apprent your path to the end of the PATH variable.

If you want to replace something already on the path, you'll want to prepend your path as follows:

````bash
export PATH=/home/username/testing:$PATH
````
To make sure that it is executed first before the path you are trying to replace.



### Increase your history size

As mentioned earlier, the `history` command is very useful for looking back at what you've previously done on a linux machine. The bashrc file defines two variables that determine how this data is stored:
````bash
HISTSIZE=1000      # the number of most recent commands that will be written to the history file
HISTFILESIZE=2000  # the number of total commands the history file will store
````

While it is probably unlikely for you to run more than 1000 commands, it's pretty easy to reach more than 2000 commands total if you frequently run commands on your server. Therefore it's a good idea to increase the `HISTFILESIZE` to something larger like 50000. Since the only data that is stored is the command, not the resulting console output, the total size of the file will still be really small.


### Enable console coloring

Adding some color to your console can greatly improve the experience of working in the console. 

newer versions of debian/ubuntu will already add some color to their output, but otherwise this is done by modifying the `PS1` variable, which is used to alter the appearance of the console string.

First, you'll want `nano ~/bashrc` and change this line
````bash
#force_color_prompt=yes
````
to:
````bash
force_color_prompt=yes
````

Then the PS1 variable should be set as follows (note that on most recent ubuntu and debian installations this is the default):

````bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
````

This will change your commandline from this:
![](https://i.imgur.com/lZgGTkz.png)
To This:
![](https://i.imgur.com/fBMAg7n.png)

Additionally, this can be used to make it easier to tell when you are using root, by showing them as a different color:

![](https://i.imgur.com/nlNTCsr.png)

To do this, first follow the same steps as above with root's `.bashrc` file with `sudo nano /root/.bashrc`. (remember to also increase its `HISTFILESIZE` if you haven't yet).

Then once color is enabled, you can change the color from green to red by changing the first `32m` in the PS1 variable to be `31m` instead.

So for your root's `.bashrc` the line would now be:

````bash
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
````


### Show git repository branch

When working with git repositories it is really useful to be able to see when you are inside a git repository, and which branch has been checked out in your working copy, like this:
![](https://i.imgur.com/jpqNnZo.png)

To do this you first need to include this function at the bottom of your `.bashrc` file, which will be used to grab the current branch string.

````bash
# Git branch in terminal prompt.
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ [\1]/'
}
````

Then you can modify the PS1 variable from this:

````bash
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
````
to this:
````bash
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\[\033[32m\]$(parse_git_branch)\[\033[00m\]\$ '
````

You'll once again want to apply that change to root's `.bashrc` file as well, and make sure to change that first `32m` to `31m` to ensure its color remains red.



