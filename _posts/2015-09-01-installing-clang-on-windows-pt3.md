---
layout: post
title: "Installing clang++ to compile and link on Windows : Part 3"
published: true
comments: true
---

<table>
  <thead>
    <tr>
      <th>Installing clang++ to compile and link on Windows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt1/>Part 1 : The Goal</a></td>
    </tr>
    <tr>
      <td><a href=http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt2/>Part 2 : Approach 1 - Clang 3.7</a></td>
    </tr>
    <tr>
      <td><em/>Part 3 : Approach 2 - MSYS2</em></td>
    </tr>
    <tr>
      <td>Part 4 : Wrapping Up (Coming Soon)</td>
    </tr>
  </tbody>
</table>


#Approach 2 - MSYS2

In this approach we'll be installing MSYS2 to create a unix-like dev environment on windows that we will install clang and gcc on. That dev environment will then be interacted with through its bash shell.

1. Download and install MSYS2 64 bit: [Download Link](http://sourceforge.net/projects/msys2/files/Base/x86_64/msys2-x86_64-20150512.exe/download) (you'd basically never need to download the 32 bit version, since the 64 bit version can install 32 bit packages)
2. After the installer completes and the MSYS2 shell appears (to re-open it, the batch file is located in  `C:\msys64\msys2_shell.bat` ). We Need to update its packages:
    - First, update the core packages: `pacman --needed -Sy bash pacman pacman-mirrors msys2-runtime`. Enter 'y' when prompted and the updates may take a few minutes.
    - IMPORTANT: close the shell and re-open it here before doing anything else.
    - Now we can update all packages with: `pacman -Su`
    - For more detailed information on MSYS2's package management see [sections 3-5 in the documentation](http://sourceforge.net/p/msys2/wiki/MSYS2%20installation/)
3. Install the 32 bit version of MinGW-w64 and Clang (currently gcc is version 5.2.0 and clang is version 3.6.2): `pacman -S mingw-w64-x86_64-clang mingw-w64-i686-clang`
4. Similar to cygwin, the windows path `C:\` drive maps to the directory `/c` in MSYS2.

###Using MSYS2 to compile with Clang/GCC

To use the 64 bit versions of clang/clang++/gcc/g++, launch the 64 bit shell batch file, located at  `C:\msys64\mingw64_shell.bat` and for the 32 bit versions use `C:\msys64\mingw32_shell.bat`. 

- so you can use them from the commandline it may make sense to add `C:\msys64` to your system PATH.



Other things to note with MSYS2:
- If you know the name of a package you can install it like this (for the [man pages](https://www.kernel.org/doc/man-pages/)):  `pacman -S man`.
- once you've installed the man pages you can use `man pacman` to read up on some of its other commands.
- If you want to find a package you can use `pacman -Ss NAME`.
- Repositories can also be browser here http://repo.msys2.org/


