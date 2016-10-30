---
published: false
layout: post
tags:
  - tutorial
  - windows
  - linux
  - cross-platform
  - make
  - premake
  - tool
comments: true
---
# Why Use a Build Configurator?

Maintaining separate Makefiles and Visual Studio Solution files for cross-platform projects, or storing Visual Studio Project files in version control such as [Git](https://git-scm.com/) can be a pain.

- Writing [Makefiles](https://www.cs.swarthmore.edu/~newhall/unixhelp/howto_makefiles.html) for all but the simplest projects is tedious without automation, and can be difficult to debug.
- Visual Studio Solution and Project Files are pre-generated, but are not designed to be easy to edit by hand, so resolving merge conflicts with them can be frustrating.

Several build tools exist that allow us to avoid these issues. [CMake](https://cmake.org/) is widely used, but tends to have a high learning curve and uses a Proprietary scripting language.

Enter [Premake5](https://premake.github.io/): A Lightweight, Open-source, Lua-based alternative to CMake.


# What is Premake?
<!-- more -->

As with CMake, Premake allows you to define the structure and contents of your project and then dynamically generate whatever build files (Makefiles, VS Solutions, Xcode Projects, etc) you need at the time.

At the core of Premake is a **premake5.lua** file that describes your project (what programming language it uses, where to find source files, what dependencies it has, etc.). ‘**premake5.lua**’ is to _**Premake**_ what is ‘**Makefile**’ is to _[GNU Make](https://www.gnu.org/software/make/)_, and what ‘**doxyfile**’ is to _[Doxygen](http://www.stack.nl/~dimitri/doxygen/)_.

Because _premake5.lua_ allows you to uniquely generate whatever build files you need in seconds, you no longer need to version them in your repository. You configure your version control to [ignore](https://git-scm.com/docs/gitignore) the build files, and version the premake5.lua file instead. Resolving merge conflicts on a premake5.lua file is far more sane than on a Visual Studio project file.

Once you have a premake5.lua file, you can run the premake executable to generate your desired project files. For example:

- To generate a Makefile on Linux you run **_./premake5 vs2015_**
- To generate a VS 2015 Solution on Windows you run **_premake5 vs2015_**

This guide will be covering Premake5, the latest version. Official documentation can be found on in a wiki on github: https://github.com/premake/premake-core/wiki. 

_**NOTE**: if you ever search the web for premake questions, and see older resources on bitbucket or the 'Industrious One' forums, please ignore them. They are old and almost certainly out of date, often referring to Premake 4._
