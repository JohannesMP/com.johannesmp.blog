---
layout: post
title: 'Installing clang++ to compile and link on Windows : Part 1'
tags:
  - clang
  - windows
  - tutorial
  - c++14
published: true
comments: true
set_id: Installing clang++ to compile and link on Windows
set_part_id: 1
set_part_title: The Problem
---


# The Problem

In the past I've struggled getting [clang](clang.llvm.org) to work on windows in as complete a capacity as it does on mac or linux.

I tried a few [online tutorials](https://yongweiwu.wordpress.com/2014/12/24/installing-clang-3-5-for-windows/) and suggested solutions on [stack overflow](http://stackoverflow.com/a/9427377/928062), but all of them had one drawback or another:

- Some solutions required you to use a separate linking step (since as of the time of this writing clang for windows does not have its own linker)
- Some solutions required massive `-I` and `-l` compiler flags so that all the proper headers and libraries were used.
- Even when some solutions mostly worked, they often were unable to handle both 32 and 64 bit.
- Some required you to build clang from source. Not a problem in its own right, but not very convenient, especially when compared to other platforms: `apt-get install clang-3.5`, `brew install llvm --with-clang --with-asan`, etc.

So over the last month I tried to come up with a reliable tutorial to get clang to compile *and* link on windows 8.1 that would avoid these issues.

<!-- more -->

# The Goal

To be acceptable, any valid solutions would:

- Be able to compile and build a simple c++ program in a single command.
- Be able to compile c++14 code
- Be able to compile code as either 32 bit or 64 bit.

So effectively, given the following program:

{% gist 8514e437d47ac2577b3b %}

the compile string would be:

    clang++ main.cpp -o main.exe -std=c++14

and it would result in a working 64 bit windows executable.

It should also be noted that at this point my focus is less about performance, efficiency, or what linker clang is using under the hood. The main focus would be ease of use.

# The Result

With some help from [Stack Overflow](http://stackoverflow.com/questions/32239122/what-do-you-need-to-install-to-use-clang-on-windows-to-build-c14-for-64-bit) I came upon two different approaches, both of which satisfy all of my original goals.


## Approach 1

![](http://i.imgur.com/UXbYbAO.png)

Works from the default windows command prompt and allows you to switch between 32 and 64 bit compiling via batch scripts that modify the path. This approach uses both the 32 and 64 bit builds of [Clang 3.7.0 (Release Candidate 3)](http://llvm.org/pre-releases/3.7.0/) and [MinGW-W64](http://sourceforge.net/projects/mingw-w64) gcc version 5.1.0 (although 4.9.3 also works).

  - If you are more comfortable using Windows than Linux, and would rather not have to use another command promt, this approach is for you.
  - **You can read about this approach in [Part 2](http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt2/)**


## Approach 2

![](http://i.imgur.com/b0ijGq1.png)

Uses [MSYS2](https://msys2.github.io/), a unix-like development environment (similar to cygwin) that uses pacman as its package manager ([just like Arch Linux](https://wiki.archlinux.org/index.php/Pacman)). I particularly like its shell which is nicer and, at least with initial testing, has been more reliable than cygwin's terminal. It also installs both the 32 and 64 bit versions which you select by choosing the 32 bit or 64 bit shell.

  - If you like using bash, having a robust package manager, and a commandline that you can resize on the fly or copy/paste into easily, this approach is for you.
  - **You can read about this approach in [Part 3](http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt3/)**


----

As an added bonus, with both of these approaches you also get the fully working gcc and g++ compilers thrown in for free.

Note that these two approaches don't conflict with each other so you are welcome to try them both, or go with the one you prefer.
