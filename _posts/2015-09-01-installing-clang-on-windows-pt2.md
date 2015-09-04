---
layout: post
title: "Installing clang++ to compile and link on Windows : Part 2"
category: programming
tags: 
  - clang
  - windows
published: true
comments: true
set_id: Installing clang++ to compile and link on Windows
set_part_id: 2
set_part_title: "Approach 1 - Clang 3.7"
---




#Approach 1 - Clang 3.7

<img src=http://i.imgur.com/UXbYbAO.png />

In this approach we'll be installing Clang 3.7.0 and gcc 5.1.0 (via MinGW-w64), to allow us to build and link with clang from the standard windows command prompt.

<!-- more -->

First quickly consider the following:

- This tutorial was created for, and tested with windows 8.1 - I can't make promises how well it will work on windows 10.
- If you have an older version of clang/LLVM installed, please use its uninstaller before continuing. Make sure that **neither** `C:\Program Files\LLVM` **or**`C:\Program Files (x86)\LLVM` exist on your machine.
- You'll need to edit your [system PATH](http://www.computerhope.com/issues/ch000549.htm), and the standard windows UI for it is pretty awful. You should consider grabbing the free *Windows Environment Variable Editor* or "Eveditor": **[http://eveditor.com/](http://eveditor.com/)** which is more user friendly and shows you if you have invalid paths set.
- You will also need to be able to open `.7z` archives, so make sure to install **[7zip](http://www.7-zip.org/)** or something similar.


Ok, let's get to the fun part:

1. Clang for 64 bit
    - **[Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win64.exe)** - clang 3.7.0 (release candidate 3) 64 bit.
    - Run the installer. When you get to the PATH settings, make sure to add LLVM to the system PATH:
    <img src=http://i.imgur.com/AIjaxKk.png />
    - Use the default install location: `C:\Program Files\LLVM`. Once the installation completes hit 'Finish'.
    - `clang++.exe` should be located in `C:\Program Files\LLVM\bin`, which should be in your system PATH.
2. Clang for 32 bit
    - **[Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win32.exe)** - clang 3.7.0 (release candidate 3) 32 bit.
    - Run the installer. **IMPORTANT**: because we technically just installed a different version of LLVM, the installer will see the 64 bit version as an 'older version' and will give you this warning:
        <img src="http://i.imgur.com/TcfUY3b.png" />
        You will want to click 'No', so that the 64 bit version doesn't get uninstalled. We want *both* the 32 *and* the 64 bit versions.
    - You will once again be prompted to add LLVM to the path. This time leave it set to `Do not add LLVM to the system PATH`.
    - Use the default install location: `C:\Program Files (x86)\LLVM` and complete the installation.
    - clang++.exe should be located in `C:\Program Files (x86)\LLVM\bin`, but should **NOT** be in your system path.
3. MinGW-w64 for 64 bit
    - **[Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/5.1.0/threads-posix/seh/x86_64-5.1.0-release-posix-seh-rt_v4-rev0.7z/download)** - MinGW-w64: 64 bit, version 5.1.0 with posix threads and seh exceptions.
    - Extract the `x86_64....7z` file, either to your desktop (and move it), or directly to your `C:` Drive.
    - Once done you should be able to find g++.exe in `C:\mingw64\bin`
    - Add `C:\mingw64\bin` to your system PATH.
4. MinGW-w64 for 32 bit
    - **[Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/5.1.0/threads-posix/dwarf/i686-5.1.0-release-posix-dwarf-rt_v4-rev0.7z/download)** -  MinGW-w64: 32 bit, version 5.1.0 with posix threads and dwarf exceptions.
    - Extract the `x686...7z` file, either to your desktop (and move it), or directly to your `C:` Drive.
    - Once done you should be able to find g++.exe in `C:\mingw32\bin`
    - **Do NOT mingw32 to your path!**
5. Batch files
    - Downloads (right click and save): **[setgcc32.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc32.bat)** and **[setgcc64.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc64.bat)**.
    - I decided that the 64 bit version of clang/g++ should be used by default, and if you want to use the 32 bit versions you can temporarily add the 32 bit versions to your path. For that I created [two batch files](https://gist.github.com/JohannesMP/1e7ed200367460255971)
    - `setgcc32.bat` prepends the 32 bit locations to the path
    - `setgcc64.bat` (which resets the path to its previous state).
    - Both batch files should be in a folder on the PATH. For example, create `C:\Utils`, place the batch files in there and add `C:\Utils` to your path.
    
And that's it! At this point, you should have the 32 bit and 64 bit versions of Clang 3.7.0 and MinGW-w64 5.1.0 installed. You can now compile 64 bit windows applications through clang like this:
  
  <img src="http://i.imgur.com/ZbQGQgT.png" />

And if you need to compile to 32 bit, just run the batch script, which will temporarily modify your path to prefer the 32 bit versions:

  <img src="http://i.imgur.com/hh0dRO2.png" />
  

Note: If you see a fatal error that some common library cannot be found when you try to compile, such as:

  <img src=http://i.imgur.com/UNerbDA.png />
    
If this happens check that:

  - `C:\mingw64\bin` exists and **IS** in your system PATH
  - `C:\mingw32\bin` exists and is **NOT** in your system PATH.









----
