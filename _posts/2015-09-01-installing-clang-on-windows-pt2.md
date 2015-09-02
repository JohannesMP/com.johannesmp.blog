---
layout: post
title: "Installing clang++ on windows so it just works : Part 2"
published: true
---


<table>
  <thead>
    <tr>
      <th>Installing clang++ on windows so it just works</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt1/>Part 1 - The Goal</a></td>
    </tr>
    <tr>
      <td><em>Part 2 - Approach 1</em></td>
    </tr>
    <tr>
      <td><a href=http://blog.johannesmp.com/2015/09/01/installing-clang-on-windows-pt3/>Part 3 - Approach 2</3></td>
    </tr>
  </tbody>
</table>


##Approach 1: Clang and MinGW-w64 installed alone and run through windows command promt

First quickly consider the following:

- If you have an older version of clang installed, please use its uninstaller now. Make sure that **neither** `C:\Program Files\LLVM` and `C:\Program Files (x86)\LLVM` exist on your machine.
- Since you'll want to be able to check the system PATH more easily than the default windows UI allows, consider grabbing the free *Windows Environment Variable Editor* or "Eveditor": **http://eveditor.com/**
- You will also need to be able to open `.7z` archives, so make sure to install **[7zip](http://www.7-zip.org/)** or something similar.


Ok, let's get to the fun part:

1. Clang for 64 bit
    - Get clang 3.7.0 (release candidate 3) 64 bit: **[Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win64.exe)**. and run its installer.
    - When you get to the PATH settings, make sure to add LLVM to the system PATH:
    <img src=http://i.imgur.com/AIjaxKk.png />
    - Use the default install location: `C:\Program Files\LLVM`. Once the installation completes hit 'Finish' At this point clang++.exe should be located in C:\Program Files\LLVM\bin, which should be in your system PATH
2. Clang for 32 bit
    - Download and clang 3.7.0 (release candidate 3) 32 bit: **[Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win32.exe)**, and run its installer.
    - **IMPORTANT**: because we technically just installed a different version of LLVM, the installer will see the 64 bit version as an 'older version' and will give you this warning:
        <img src="http://i.imgur.com/TcfUY3b.png" />
        You will want to click 'No', so that the 64 bit version doesn't get uninstalled. We want *both* the 32 *and* the 64 bit versions.
    - You will once again be prompted to add LLVM to the path. This time leave it set to `Do **not** add LLVM to the system PATH`.
    - Use the default install location: `C:\Program Files (x86)\LLVM`
    - Once the installation completes hit 'Finish' At this point clang++.exe should be located in C:\Program Files (x86)\LLVM\bin, but should **NOT** be in your system path
3. MinGW-w64 for 64 bit
    - Download MinGW-w64: 64 bit, version 5.1.0 with posix threads and seh exceptions: **[Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/5.1.0/threads-posix/seh/x86_64-5.1.0-release-posix-seh-rt_v4-rev0.7z/download)**
    - Extract the `x86_64....7z` file to your root drive so that you have `C:\mingw64`
    - Add `C:\mingw64\bin` to your system PATH.
4. MinGW-w64 for 32 bit
    - Download MinGW-w64: 32 bit, version 5.1.0 with posix threads and dwarf exceptions: **[Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/5.1.0/threads-posix/dwarf/i686-5.1.0-release-posix-dwarf-rt_v4-rev0.7z/download)**
    - Extract the `x86_64....7z` file to your root drive so that you have `C:\mingw34`
    - Do NOT add anything to your path.
5. Batch files
    - Clang is hard-coded to look for several versions of mingw32/cygwin/etc. based on what it finds first in the path. Unfortunately the order matters, and it gets confused if it finds the wrong version first.
    - I decided that, 64 bit should be the default, and if you want to use the 32 bit versions you can use batch files to temporarily add the 32 bit locations to the path. For that I created [two batch files](https://gist.github.com/JohannesMP/1e7ed200367460255971), **[setgcc32.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc32.bat)** (which prepends the 32 bit locations to the path) and **[setgcc64.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc64.bat)** (which resets the path to its previous state). Both batch files should be placed in the path, for example in  C:\Utils .
    
And that's it! At this point, you should have the 32 bit and 64 bit versions of Clang 3.7.0 and MinGW-w64 5.1.0 installed. You can now compile 64 bit windows applications through clang like this:
    <img src="http://i.imgur.com/mF9Z3Ij.png" />

Note: if you see a fatal error that some common library cannot be found when you try to compile, make sure that Only the 64 bit version of LLVM is on the PATH and `C:\mingw64\bin` should be on the path:
    <img src=http://i.imgur.com/UNerbDA.png />









----
