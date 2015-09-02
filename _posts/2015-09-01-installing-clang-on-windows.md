---
layout: post
title: Installing clang++ on windows so it just works.
published: false
---

In my experience using clang to compile _and_ link a small c or c++ program on mac or linux has been fairly straightforward: You use your distro's package manager and simply ask it to install clang, and are then able to just build and link in one step:

    clang++ main.cpp -o main_osx -std=c++14
    
I recently wanted to figure out a reliable way to use compile on windows as simply as I've been able to on mac and linux.

Basically, given the following program:

    #include <iostream>

    int main()
    {
        int arr[] = {1, 2, 3, 4, 5};
        for(auto el : arr)
        {
            std::cout << "hello world" << std::endl;
        }
        return 0;
    }