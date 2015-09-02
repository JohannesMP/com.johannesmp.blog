---
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
    - If you are more comfortable using Windows than Linux, and would rather not have to use another command promt, this approach is for you.
    - **You can read about this approach in Part 2**
    
<img src=http://i.imgur.com/b0ijGq1.png />
2. The second approach uses [MSYS2](https://msys2.github.io/), a unix-like development environment (similar to cygwin) that uses pacman as its package manager ([just like Arch Linux](https://wiki.archlinux.org/index.php/Pacman)). I particularly like its shell which is nicer and, at least with initial testing, has been more reliable than cygwin's terminal. It also installs both the 32 and 64 bit versions which you select by choosing the 32 bit or 64 bit shell.
    - If you like using bash, having a robust package manager, and a commandline that you can resize on the fly or copy/paste into easily, this approach is for you.
    - **You can read about this approach in Part 3**

As an added bonus, in both of these approaches you also get the fully working gcc and g++ compilers thrown in for free.

Note that these two approaches don't conflict with each other so you are welcome to try them both, or go with the one you prefer.
