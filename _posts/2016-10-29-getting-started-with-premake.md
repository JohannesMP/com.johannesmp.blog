---
published: true
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
This initial post will cover what Premake is, why you should use it, and provide an example project you can download and try out for yourself.

Future posts will expand on the provided example by walking through the process of setting up more complicated premake configurations, such as cross-platform libraries and dependencies.


## Why Use a Build Configurator?

Maintaining separate _Makefiles_ and _Visual Studio Solution_ files for cross-platform projects, or storing _Visual Studio Project_ files in version control such as [Git](https://git-scm.com/) can be a pain.

- Writing [Makefiles](https://www.cs.swarthmore.edu/~newhall/unixhelp/howto_makefiles.html) for all but the simplest projects is tedious without automation, and can be difficult to debug.
- Visual Studio Solution and Project Files are pre-generated, but are not designed to be easy to edit by hand, so resolving merge conflicts with them can be frustrating.

Several build tools exist that allow us to avoid these issues. [CMake](https://cmake.org/) is widely used, but tends to have a high learning curve and uses a Proprietary scripting language.

This post will discuss [Premake5](https://premake.github.io/): A Lightweight, Open-source, Lua-based alternative to CMake.

<br />
## What is Premake?
<!-- more -->

As with CMake, Premake allows you to define the structure and contents of your project and then dynamically generate whatever build files (Makefiles, VS Solutions, Xcode Projects, etc) you need at the time.

At the core of Premake is a **premake5.lua** file that describes your project (what programming language it uses, where to find source files, what dependencies it has, etc.). ‘**premake5.lua**’ is to _**Premake**_ what is ‘**Makefile**’ is to _[GNU Make](https://www.gnu.org/software/make/)_, and what ‘**doxyfile**’ is to _[Doxygen](http://www.stack.nl/~dimitri/doxygen/)_.

Because _premake5.lua_ allows you to uniquely generate whatever build files you need in seconds, you no longer need to version them in your repository. You configure your version control to [ignore](https://git-scm.com/docs/gitignore) the build files, and version the premake5.lua file instead. Resolving merge conflicts on a premake5.lua file is far more sane than on a Visual Studio project file.

Once you have a premake5.lua file, you can run the premake executable to generate your desired project files. For example:

- To generate a Makefile on Linux you run `./premake5 vs2015`
- To generate a VS 2015 Solution on Windows you run `premake5 vs2015`

This guide will be covering Premake5, the latest version. Official documentation can be found on in a wiki on github: https://github.com/premake/premake-core/wiki. 

_**NOTE**: if you ever search the web for premake questions, and see older resources on bitbucket or the 'Industrious One' forums, please ignore them. They are old and almost certainly out of date, often referring to Premake 4._

<br />
## A Simple Premake Example

To help with the learning curve, I’ve provided you with an example Premake5 Project that will compile a simple ‘hello world’ program for Linux, MacOS and Windows: [https://github.com/JohannesMP/Premake-for-Beginners](https://github.com/JohannesMP/Premake-for-Beginners)

To use premake you will need to be familiar with basic LUA syntax. You will not need to install anything to make LUA work for premake - It is entirely contained in the tiny premake5.exe executable. 

If you have never used LUA before, you can refer to the [LearnXinYminutes LUA page](https://learnxinyminutes.com/docs/lua/), or [The Lua Crash Course](http://luatut.com/crash_course.html).  I have also included a heavily commented version of premake5.lua which covers the relevant basics. You will not need to know more than basic LUA syntax.

#### Run Premake
- Download the repository to your prefered location.
  - `Build` contains the premake files and binaries. Please note that the binaries are included for convenience - you can delete them if you copy the one for your OS to a location on your PATH.
    - `Build/premake5.lua` describes the project and allows you to generate the Project files you want.
    - `Build/premake5.lua_commented.lua` is the same file, but heavily commented for those that have never used LUA before, and goes into more detail explaining what premake's functions do.
  - `Source` contains the source code, in this case a simple hello world program in C++.
  

#### Build a Visual Studio project on Windows
- cd into the `Build` folder _(running `dir` should list the premake5.lua file)_
- Run `premake5 vs2015` – this will generate a `project_vs2015` folder containing the VS solution and project files.
- You can double click the `.sln` file to open the project in visual studio and compile it.

#### To Build a Makefile on Mac or Linux
- cd into the `Build` folder _(running `ls -la` should list the premake5.lua file)_
- Run `./premake5 gmake` (or `./premake5_mac` for mac) – this will generate a `project_gmake` folder containing two Makefiles.
  - You get one master `Makefile`, and one `PROJECTNAME.make` file per project.
  - If you run `make help` the console output will tell you how to build specific configurations. For example, to build a 32bit release you run `make config=release_x32`
  - For debugging, you can have verbose output by running `make verbose=1 ...`
- cd into the project folder and run `make`

#### Check out the Output
- For both Windows and Linux, the output directory containing your compiled binary will be named `bin_CONFIGURATION` based on your selected configuration (Release/Debug, 32/64 bit). 
- For example, it will be named `bin_Debug_x64` for 64bit Debug.

Please read through the premake5.lua_commented.lua file for a detailed breakdown of everything that is happening in the file.


## Further Examples

- The hello world program on the official Wiki: https://github.com/premake/premake-core/wiki/Your-First-Script
- A more advanced project that uses dependencies (GLFW to render an opengl window) that should work cross-platform: https://github.com/JohannesMP/premake5_crossplatform_example
