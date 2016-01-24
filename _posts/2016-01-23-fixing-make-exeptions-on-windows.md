---
layout: post
title: "Fixing Make Exception on Windows"
tags: 
  - make
  - windows
  - fix
  - snippet
published: true
comments: true
---

# The Problem

Some versions of gnu `make` for windows can encounter an exception when you try to run a make file with them:

![make: Interrupt/Exception caught (code = 0xc0000000, addr = 0x00000000)](https://i.imgur.com/njKsqHr.png)

I encountered this issue, and eventually stumbled upon a [stack overflow question](http://superuser.com/questions/375029/make-interrupt-exception-caught), the top answer of which recommended explicitly passing SHELL variable. This did indeed fix the problem for me:

![SHELL=C:/Windows/System32/cmd.exe](https://i.imgur.com/OtuThaZ.png)

## Solution 1 - The Quick Fix

> When running make, pass in this SHELL variable:
> 
>     SHELL=C:/Windows/System32/cmd.exe
>
> Either along with the command line arguments, or just add it to the top of the make file. 
>

This solution works, but doesn't fix the actual problem: why is `make` unable to find the shell?

<!-- more -->

In the comments of the stack overflow post some people mentioned that the problem might stem from the system `PATH`. I ended up switching to a [newer version of make (4.1)](http://www.equation.com/servlet/equation.cmd?fa=make) which, while it did not fix the problem, handled the exception more gracefully and sheds some light on what is actually happening:

![sh: C:\Program: No such file or directory](https://i.imgur.com/zZ1PJU4.png)

It displays an error that states `sh: C:\Program: No such file or directory`.

Basically each instance of "Program Files" on the `PATH` contains a space that isn't escaped properly (as far as `make` is concerned). It is not the length of the path, but the spaces that are causing this issue. 
Indeed, the top of my path looks like this:

![PATH before fix](https://i.imgur.com/ONlEFaT.png)

(For anyone curious, I'm using the free [Environment Variable Editor](http://eveditor.com/) to make managing my `PATH` much more painless)

While I *could* move `%SystemRoot%\system32` to appear before an instance of program files, I already deliberately have it lower so that I can use a newer version of Git, which is in program files.

A Better solution is to change the `PATH` to no longer include spaces. A clever solution also recommended on stack overflow was to use macros instead:

## Solution 2 - Modify your PATH

> Replace every instance of `Program Files` with `PROGRA~1` and `C:\Program Files (x86)` with `PROGRA~2`

So my `PATH` now looks like this:

![PATH after fix](https://i.imgur.com/odFwFkm.png)

This way I am able to maintain the order of my path, and don't have to add an additional step every time I want to build a make file.

# Summary

Some people may not have a lot of things on their path, in which case I would strongly suggest that you just move system32 to the top. However if you can't easily do that, I hope these two solutions work for you.
