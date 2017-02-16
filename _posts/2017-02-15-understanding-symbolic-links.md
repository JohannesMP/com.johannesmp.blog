---
title: Understanding Symbolic Links
published: false
---

Symbolic links, or *"Symlinks"* are a fundamental tool in the Linux arsenal.

They are used ***everywhere*** in Linux and are exceedingly useful in organizing configuration files as well as speeding up your workflow.

Symlinks allow you to make a file or folder accessible as if by reference at another location.

{% include alert.html type="info" message="This post assumes you have a [basic grasp of Linux commands](http://linuxcommand.org/learning_the_shell.php) such as [apt-get](https://help.ubuntu.com/community/AptGet/Howto) and [linux file permissions](https://help.ubuntu.com/community/FilePermissions)." %}

<!-- more -->

## Symbolic link Examples

For example, I use the gcc compiler on a linux server. I have both version 4.6 as well as version 4.9 installed, which I can run with `gcc-4.6`and `gcc-4.9` respectively:

![](https://i.imgur.com/JrL2EYB.png)

But I can also run gcc via just `gcc`, which defaults to version 4.9:

![](https://i.imgur.com/00jrgJB.png)

So how does Linux know which version to run? are there two copies of the same program on my computer under different names?

If we look in the directory that contains gcc (which we can find by running `which gcc`) we will see the following:

![](https://i.imgur.com/JVSc2zr.png)

The *turqoise* text represents a symbolic link, followed by an arrow that shows what the link is linking to. In this case, the symbolic link is named `gcc` and pointing to the file `gcc-4.9`

------

To help understand exactly how this works, let's take a step back and look at another example. If you've used C++ you should be familiar with the concept of a reference:

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

If we print the contents of the file to our  screen with the [`cat` command](http://www.linuxtechi.com/cat-command-examples-for-beginners-in-linux/), we can see that both the original file as well as the symlink:

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
