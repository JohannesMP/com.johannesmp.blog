---
layout: post
title: Installing clang++ on windows so it just works.
published: false
---


In the past I've struggled getting [clang](clang.llvm.org) to work on windows in as complete a capacity as it does when I'm working on mac or linux.

I tried a few [online tutorials](https://yongweiwu.wordpress.com/2014/12/24/installing-clang-3-5-for-windows/) and suggested solutions on [stack overflow](http://stackoverflow.com/a/9427377/928062), but all of them had one drawback or another:

- Some solutions would require you to use a separate linking step (since as of the time of this writing clang for windows does not have its own linker)
- Some solutions required massive `-I` and `-l` compiler flags so that all the proper headers and libraries were used.
- And even when some solutions mostly worked, they often were unable to handle both 32 and 64 bit.

So over the last month I tried to come up with a reliable tutorial to get clang to compiling *and* linking on windows that would avoid these issues.

#The Goal

To be acceptable, any valid solutions would:

- Be able to compile and build a simple c++ program in a single command. So effectively, given the following program:

        #include <iostream>
        #include <vector>
    
        int main()
        {
            std::vector<int> vect {1, 2, 3, 4, 5};
            for(auto el : vect)
            {
                std::cout << " - " << el << std::endl;
            }
            return 0;
        }
    
	the compile string would be:

    	clang++ main.cpp -o main.exe -std=c++14
    
	and it would result in a working 64 bit windows executable.
    
- Be able to compile c++14 code
- Be able to compile code as either 32 bit or 64 bit.

It should also be noted that at this point my focus is less about performance, efficiency, or what linker clang is using under the hood. The main focus would be ease of use.

#The Result

<img src=http://i.imgur.com/OOZjZd1.gif />

I came up with two separate approaches:

1. The first approach (as seen in the [above gif](http://i.imgur.com/OOZjZd1.gif)) works from the default windows commandline and allows you to switch between 32 and 64 bit compiling batch scripts that modify the path. This approach uses both the 32 and 64 bit builds of [clang 3.7.0 (pre-release)](http://llvm.org/pre-releases/3.7.0/) and [MinGW-W64](http://sourceforge.net/projects/mingw-w64) gcc version 5.1.0 (although 4.9.3 also works).
2. The second approach uses [MSYS2](https://msys2.github.io/), a unix-like development environment (similar to cygwin) that uses pacman as its package manager. I particularly like its shell which is nicer and, at least with initial testing, has been more reliable than cygwin's terminal. It also installs both the 32 and 64 bit versions which you select by choosing the 32 bit or 64 bit shell.

Note that these two approaches don't conflict with each other so you are welcome to try them both:


##Approach 1: Clang and MinGW-w64 installed alone and run through windows command promt

First quickly consider the following:

- If you have an older version of clang installed, please use its uninstaller now. Make sure that **neither** `C:\Program Files\LLVM` and `C:\Program Files (x86)\LLVM` exist on your machine.
- Since you'll want to be able to check the system PATH more easily than the default windows UI allows, consider grabbing the free *Windows Environment Variable Editor* or "Eveditor": http://eveditor.com/
- You will also need to be able to open `.7z` archives, so make sure to install [7zip](http://www.7-zip.org/) or something similar.


Ok, let's get to the fun part:

1. Clang for 64 bit
    - Get clang 3.7.0 (release candidate 3) 64 bit: [Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win64.exe). and run its installer.
    - When you get to the PATH settings, make sure to add LLVM to the system PATH:
    <img src=http://i.imgur.com/AIjaxKk.png />
    - Use the default install location: `C:\Program Files\LLVM`. Once the installation completes hit 'Finish' At this point clang++.exe should be located in C:\Program Files\LLVM\bin, which should be in your system PATH
2. Clang for 32 bit
    - Download and clang 3.7.0 (release candidate 3) 32 bit: [Download Link](http://llvm.org/pre-releases/3.7.0/rc3/LLVM-3.7.0-rc3-win32.exe), and run its installer.
    - **IMPORTANT**: because we technically just installed a different version of LLVM, the installer will see the 64 bit version as an 'older version' and will give you this warning:
        <img src="http://i.imgur.com/TcfUY3b.png" />
        You will want to click 'No', so that the 64 bit version doesn't get uninstalled. We want *both* the 32 *and* the 64 bit versions.
    - You will once again be prompted to add LLVM to the path. This time leave it set to `Do **not** add LLVM to the system PATH`.
    - Use the default install location: `C:\Program Files (x86)\LLVM`
    - Once the installation completes hit 'Finish' At this point clang++.exe should be located in C:\Program Files (x86)\LLVM\bin, but should **NOT** be in your system path
3. MinGW-w64 for 64 bit
    - Download MinGW-w64: 64 bit, version 5.1.0 with posix threads and seh exceptions: [Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/5.1.0/threads-posix/seh/x86_64-5.1.0-release-posix-seh-rt_v4-rev0.7z/download)
    - Extract the `x86_64....7z` file to your root drive so that you have `C:\mingw64`
    - Add `C:\mingw64\bin` to your system PATH.
4. MinGW-w64 for 32 bit
    - Download MinGW-w64: 32 bit, version 5.1.0 with posix threads and dwarf exceptions: [Download Link](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/5.1.0/threads-posix/dwarf/i686-5.1.0-release-posix-dwarf-rt_v4-rev0.7z/download)
    - Extract the `x86_64....7z` file to your root drive so that you have `C:\mingw34`
    - Do NOT add anything to your path.
5. Batch files
    - Clang is hard-coded to look for several versions of mingw32/cygwin/etc. based on what it finds first in the path. Unfortunately the order matters, and it gets confused if it finds the wrong version first.
    - I decided that, 64 bit should be the default, and if you want to use the 32 bit versions you can use batch files to temporarily add the 32 bit locations to the path. For that I created [two batch files](https://gist.github.com/JohannesMP/1e7ed200367460255971), [setgcc32.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc32.bat) (which prepends the 32 bit locations to the path) and [setgcc64.bat](https://gist.github.com/JohannesMP/1e7ed200367460255971/raw/61be418a184e6c0ab0ef36d07c5552d38a5670cf/setgcc64.bat) (which resets the path to its previous state). Both batch files should be placed in the path, for example in  C:\Utils .
    
And that's it! At this point, you should have the 32 bit and 64 bit versions of Clang 3.7.0 and MinGW-w64 5.1.0 installed. You can now compile 64 bit windows applications through clang like this:
<img src="http://i.imgur.com/mF9Z3Ij.png" />

- Note: if you see a fatal error that some common library cannot be found when you try to compile, make sure that Only the 64 bit version of LLVM is on the PATH and `C:\mingw64\bin` should be on the path:
    <img src=http://i.imgur.com/UNerbDA.png />
    
----

##Approach 2: Clang and MinGW-w64 via MSYS2

1. Download and install MSYS2 64 bit: [Download Link](http://sourceforge.net/projects/msys2/files/Base/x86_64/msys2-x86_64-20150512.exe/download) (you'd basically never need to download the 32 bit version, since the 64 bit version can install 32 bit packages)
2. After the installer completes and the MSYS2 shell appears (to re-open it, the batch file is located in  `C:\msys64\msys2_shell.bat` ). We Need to update its packages:
    - First, update the core packages: `pacman --needed -Sy bash pacman pacman-mirrors msys2-runtime`. Enter 'y' when prompted and the updates may take a few minutes.
    - IMPORTANT: close the shell and re-open it here before doing anything else.
    - Now we can update all packages with: `pacman -Su`
    - For more detailed information on MSYS2's package management see [sections 3-5 in the documentation](http://sourceforge.net/p/msys2/wiki/MSYS2%20installation/)
3. Install the 32 bit version of MinGW-w64 and Clang (currently gcc is version 5.2.0 and clang is version 3.6.2):
    - 64 bit: `pacman -S mingw-w64-x86_64-clang`
    - 32 bit: `pacman -S mingw-w64-i686-clang `
4. Similar to cygwin, the windows path `C:\` drive maps to the directory `/c` in MSYS2.
5. To use the 64 bit versions launch the 64 bit shell batch file, located at  `C:\msys64\mingw64_shell.bat` and for the 32 bit versions use `C:\msys64\mingw32_shell.bat`.

    












----
