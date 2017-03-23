---
published: true
title: Understanding Symbolic Links
layout: post
tags:
  - linux
  - tutorial
  - server
comments: true
set_id: Linux Server Basics
set_part_id: 3
set_part_title: Understanding Symbolic Links
note_class: alert alert-info
note_prefix: Note
note_body: >-
  This post is reference [for a
  class](https://www.digipen.edu/coursecatalog/#CS261). It is assumed that you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) and [linux file permissions](https://help.ubuntu.com/community/FilePermissions), as well as some of the [SSH workflow tips covered previously](http://blog.johannesmp.com/2017/02/11/ssh-workflow-tips/).
---


![](http://i.imgur.com/IKCybXy.png)

Symbolic links, or *"Symlinks"* are a fundamental part of the Linux toolbox. They allow you to make a file or folder accessible from another location as if by reference.

Symbolic links are used ***everywhere*** in Linux. Many applications like [apache2](https://httpd.apache.org/) and [nginx](https://www.nginx.com/resources/wiki/) require them for their configuration files, and they can be a powerful tool for your workflow.



<!-- more -->


<br />
## What are Symbolic Links?

When using compilers such as [gcc](http://pages.cs.wisc.edu/~beechung/ref/gcc-intro.html), It is not uncommon to have multiple versions installed for different projects. On one of my servers I have both version **4.6** as well as version **4.9** installed, which can be run with **`gcc-4.6`** and **`gcc-4.9`** respectively:

![](https://i.imgur.com/JrL2EYB.png)

But I can also run gcc via just **`gcc`**, which defaults to version 4.9:

![](https://i.imgur.com/00jrgJB.png)

So how does Linux know which version to run? Are there two copies of the same program installed under different names?

When looking in the directory that contains gcc (which we can find by running **`which gcc`**) we will see the following in the output of **`ls -l`**:

![](https://i.imgur.com/JVSc2zr.png)

It turns out that **`gcc`** is not actually its own executable file, but instead is a symbolic link that points to **`gcc-4.9`**.  

Assuming you've [enabled color in your .bashrc file](http://blog.johannesmp.com/2017/02/11/ssh-workflow-tips/#enable-console-coloring), the **turquoise** text represents a symbolic link, followed by an arrow that shows what the link is linking to. In this case, the symbolic link is named **`gcc`** and linking to the file **`gcc-4.9`**

------

To help understand exactly how this works, let's take a step back and look at another example. If you've used *C++* you should be familiar with the concept of a *reference*:


```c++
int    a = 1;
int &r_a = a;         // storing a reference to `a`

cout <<   a << endl;  // 1
cout << r_a << endl;  // 1

r_a = 5;              // modifying `a` via the reference

cout <<   a << endl;  // 5
cout << r_a << endl;  // 5
```

A Symbolic link is very similar to a reference. For the user touching the reference, they don't know or care that **`r_a`** is effectively a proxy and not its own discrete location in memory. They are modifying it as if it was its own `int`.

Let's try re-creating this example with symlinks.

<br />
## Creating a Symbolic Link

To create a symbolic link we use the [**`ln`** command](https://en.wikipedia.org/wiki/Ln_(Unix) ) with the **`-s`** flag. The basic format is:

```bash
ln -s <path/to/source> <path/to/link>
```

To mirror the reference example above, let's create **`a.txt`** and a symbolic link named **`s_a.txt`**:

```bash
echo "1" > a.txt      # 'a.txt' is a text file that contains '1'
ln -s a.txt s_a.txt   # 's_a.txt' is a symbolic link to 'a.txt'
```

![](https://i.imgur.com/G4ROeXb.png)

We can now access the data, as well as modify it through the symlink:

![](https://i.imgur.com/83gpBiA.png)

You can also create a symbolic link to a directory, then interact with it as if it was a real folder:
![](https://i.imgur.com/d1bS1T8.png)

A symbolic link is technically neither a file nor a directory. How it is interpreted depends only on the path it was created with, and what that path contains when the symbolic link is evaluated.


<br />
## How symlinks are evaluated

When you create a symbolic link, the only thing it stores is the  ***path/to/source*** you provided. Whether a symbolic link represents a file, a directory, or nothing (a path that doesn't exist), is not decided until the exact moment you attempt to access it. We can observe that as follows:

Start with symbolic link **`s_b`** pointing to a directory named **`b`**:

![](https://i.imgur.com/S0P2mgk.png)

If we remove the original directory **`b`**, the symbolic link is now invalid (as you can see by the **red** color), and cannot be accessed:

![](https://i.imgur.com/YMj9kc2.png)

If we now create a text file named **`b`** then the same symbolic link **`s_b`** will now point to that file:

![](https://i.imgur.com/qwp4Aoj.png)

This only works because **`s_b`** was created using path **`b`**.

If instead we had created it with the path **`b/`**:
```bash
ln -s b/ s_b
```
Then the path stored would only ever evaluate to a directory, since files cannot contain the **`/`** character:

![](https://i.imgur.com/o4QkdjG.png)


<br />
## Using symlinks in your workflow
Symbolic links have many useful applications. Here are a few examples:

### Easy Versioning
If you recall the *gcc* example above, it is not uncommon to want to access the latest version of a file or directory without having to explicitly state the version number.

For example, let's say you have several versions of your app: **`my_app_v1/`**, **`my_app_v2/`**, **`my_app_v3/`** and want to have a webserver only host the latest version. 

You can create a symbolic link called **`my_app_latest`** which points latest version you need.  That way you can just always point your webserver to **`my_app_latest/`**:

![](https://i.imgur.com/rU1tjs5.png)

When the time comes to install **`my_app_v4/`**, you can remove the old symlink with **`rm`** and re-create it, without having to restart the webserver:

![](https://i.imgur.com/SjCNqmY.png)

In this case, manually swapping out the symbolic link would be much easier than updating the webserver's configuration file, especially if multiple other applications all rely on **`my_app`**.

With web servers like *apache2* or *nginx* you would have to reload a config file after changing it, but this way there is no need to restart them, since as far as they are concerned, nothing has actually changed.


### Config File Management
Most Applications on Linux store their configuration files in **`/etc/<appname>`**.

For example, *mysql* might store its configuration files in **`/etc/mysql/`**,<br/>
*php5* could have its files in **`/etc/php5/apache2/`**, etc.

While you could modify each file on its own as necessary, this is not very maintainable in the long run:

- If you ever want to set up a similar configuration on another server, you'll have to hunt for the files.
- If you ever need to revert a configuration to a previous state, you will have no way to do so.


Ideally you would really want to use version control like [git](https://en.wikipedia.org/wiki/Git)  so you could store your configurations, revert them, and clone them on other machines as needed, but the files are all spread around.

That is where Symbolic links come in.

If you set up a single directory for your app's files, such as **`/opt/my_app/`**, you can set up a git repo for its config files, located in (for example) **`/opt/my_app/configs/`**.

You would move the config files you need into the repository, then create a symlink back in the original location:

```bash
cd /etc/example
sudo mv file.conf /opt/my_app/configs/etc_example/
sudo ln -s /opt/my_app/configs/etc_example/file.conf
```

You can store configs in your repo under subdirectories that mirror their location in **`/etc/...`**. This makes it really easy to know where the config files should be symlinked to when you later clone this repo on a new server.

Furthermore, several Linux applications that have config files in **`/etc/`** have the ability to load additional configuration files from a directory. 

For example, while the default configuration file for *php5* might be **`/etc/php5/apache2/php.ini`**, it also provides a directory **`/etc/php5/apache2/conf.d/`**, and any config file stored there (including symlinks) will be loaded after the default config file.

This lends itself really well to symlinks by design, and is demonstrative of Linux design in general. You can store all of your configuration files in one place, and then only symlink the ones you need on a given server.





