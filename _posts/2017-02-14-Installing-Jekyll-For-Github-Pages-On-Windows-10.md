---
published: false
layout: post
tags:
  - windows
  - github pages
  - jekyll
comments: true
---
This blog is hosted on github pages using jekyll, a blog-aware static website generator.

While there several guides for installing jekyll for windows ([official github pages](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/), [just jekyll](http://jekyll-windows.juthilo.com)) They seem to skip several key points that have given me trouble when attempting to install the latest version of ruby (2.3.3 as of this writing) on windows 10.

Specifically there are several errors with the `jekyll serve` command that don't seem to be addressed anywhere except in obscure git issue postings. Having struggled through this twice now on two different machines, I'm now making this guide so that at least I'll be able to refer to it quickly.

This guide assumes that you know how to modify the PATH and set environment variables. I highly recommended the free [Windows Environment Variables Editor](http://eveditor.com/) which greatly improves that experience over the default Windows Environment variable UI, even in Windows 10.


### Install Ruby

![](https://i.imgur.com/oRqtG1N.png)

The latest direct download link for 64 bit as of this writing is: [rubyinstaller-2.3.3-x64.exe](https://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.3.3-x64.exe) *(located on the download page [in the top **RubyInstallers** section](http://rubyinstaller.org/downloads/)*.

Run the installer, and install in the default location. For 2.3.3 64bit this would be `C:\Ruby23-x64`

During the installation make sure to have ruby added to the path, or do so manually by adding `C:\Ruby23-x64\bin` to your path.

You can verify that your path has been modified correctly by opening a new command prompt and typing (a command prompt that was open previously will not yet have the new PATH):

````
ruby --version
````


## Install Ruby Development Kit
![](https://i.imgur.com/iRX53qj.png)

The latest direct download link as of this writing is: [DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe](https://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe) *located on the download page [in the **Other Useful Downloads > DEVELOPMENT KIT** section](http://rubyinstaller.org/downloads/)*.

{% include alert.html message="**NOTE**: If you manually downloaded the latest version from the download page, make sure both are 64 bit or not, and make sure that the dev kit is the latest version, since it lists the latest version at the *bottom* for some reason, instead of the *top* like the normal ruby download." type="warning" %}

When you run the executable it will simply ask you for a directory to extract to. **DO NOT EXCTRACT TO YOUR DOWNLOADS FOLDER**, which will dump dozens of files. Instead provide a path next to the ruby installation such as `C:\RubyDevKit`.

You need to first initialize and then bind the devkit to your Ruby instllation. Open a command prompt and navigate to the newly extracted folder:

````
cd C:\RubyDevKit
````

Then to initialize the devkit run:

````
ruby dk.rb init
````

Finally install and bind the devkit to your ruby installation:

````
ruby dk.rb install
````


## Create a new 
