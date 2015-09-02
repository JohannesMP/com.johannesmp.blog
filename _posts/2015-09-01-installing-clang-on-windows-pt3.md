---
layout: post
title: "Installing clang++ on windows so it just works : Part 3"
published: true
---


##Approach 2: Clang and MinGW-w64 via MSYS2

1. Download and install MSYS2 64 bit: [Download Link](http://sourceforge.net/projects/msys2/files/Base/x86_64/msys2-x86_64-20150512.exe/download) (you'd basically never need to download the 32 bit version, since the 64 bit version can install 32 bit packages)
2. After the installer completes and the MSYS2 shell appears (to re-open it, the batch file is located in  `C:\msys64\msys2_shell.bat` ). We Need to update its packages:
    - First, update the core packages: `pacman --needed -Sy bash pacman pacman-mirrors msys2-runtime`. Enter 'y' when prompted and the updates may take a few minutes.
    - IMPORTANT: close the shell and re-open it here before doing anything else.
    - Now we can update all packages with: `pacman -Su`
    - For more detailed information on MSYS2's package management see [sections 3-5 in the documentation](http://sourceforge.net/p/msys2/wiki/MSYS2%20installation/)
3. Install the 32 bit version of MinGW-w64 and Clang (currently gcc is version 5.2.0 and clang is version 3.6.2): `pacman -S mingw-w64-x86_64-clang mingw-w64-i686-clang`
4. Similar to cygwin, the windows path `C:\` drive maps to the directory `/c` in MSYS2.
5. To use the 64 bit versions launch the 64 bit shell batch file, located at  `C:\msys64\mingw64_shell.bat` and for the 32 bit versions use `C:\msys64\mingw32_shell.bat`.

    


