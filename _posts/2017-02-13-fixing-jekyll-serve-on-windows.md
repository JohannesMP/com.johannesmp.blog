---
published: true
layout: post
tags:
  - windows
  - fix
  - github-pages
  - jekyll
  - ruby
  - bundler
comments: true
---

![](http://i.imgur.com/KvPMbo9.png)

This blog is hosted on [github pages](https://pages.github.com/) using [jekyll](https://jekyllrb.com/), a static website generator.

While there are several guides for installing jekyll for Windows ([official github pages article](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/), [Run Jekyll on Windows](http://jekyll-windows.juthilo.com)) They seem to skip some key points that have given me trouble with the latest version of ruby (2.3.3 as of this writing) on Windows 10.

Having struggled through this twice now on two separate Windows installations, I'm making this guide so that at least I'll be able to refer to it quickly. I hope it helps anyone else that has issues getting `jekyll serve` to just work as intended.

<!-- more -->

### What This guide covers:
- The setup required to serve existing jekyll projects on Windows 10.
- Errors while compiling the json gem in Ruby.
- Errors regarding SSL authentication when trying to run `jekyll serve`.
- Errors regarding Github Authentication when trying to run `jekyll serve`.

### What this guide does *NOT* cover:
- How to create a new Jekyll project or how to use Github pages. 

If you would like to learn how to make a new Github Pages Jekyll Project [please see step 2 and 3 in the official github pages guide](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-1-create-a-local-repository-for-your-jekyll-site), or look into other tutorials. For example:

  - [https://blog.webjeda.com/](https://blog.webjeda.com/)
  - [http://jmcglone.com/guides/github-pages/](http://jmcglone.com/guides/github-pages/)
  - [https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/)

<br />

# 1. Install all Jekyll Dependencies

First we need to make sure that we have all dependencies for Jekyll on Windows 10. If you have *Ruby*, *Ruby Development Kit* and *Bundler* already installed and working, feel free to skip down to the **2. Get *Jekyll Server* Working** section below.

This guide assumes that you know how to modify the PATH and set environment variables. I highly recommended the free [Windows Environment Variables Editor](http://eveditor.com/) which greatly improves that experience over the default Windows Environment variable UI, even in Windows 10.

## Install Ruby

![](https://i.imgur.com/oRqtG1N.png)

The latest direct download link for 64 bit as of this writing is: [rubyinstaller-2.3.3-x64.exe](https://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.3.3-x64.exe)  *(located on the download page [in the top **RubyInstallers** section](http://rubyinstaller.org/downloads/))*.

Run the installer, and install in the default location. For 2.3.3 64bit this would be `C:\Ruby23-x64`

During the installation make sure to have ruby added to the path, or do so manually by adding `C:\Ruby23-x64\bin` to your path.

![](https://i.imgur.com/6vfhgPb.png)

You can verify that your path has been modified correctly by opening a new command prompt and typing (a command prompt that was open previously will not yet have the new PATH):

```
ruby --version
```


## Install Ruby Development Kit

![](https://i.imgur.com/iRX53qj.png)

The latest direct download link as of this writing is: [DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe](https://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe)  *(located on the download page [in the **Other Useful Downloads > DEVELOPMENT KIT** section](http://rubyinstaller.org/downloads/))*.

{% include alert.html message="**NOTE**: If you manually downloaded the latest version from the download page, make sure both are 64 bit or not, and make sure that the dev kit is the latest version, since it lists the latest version at the *bottom* for some reason, instead of the *top* like the normal ruby download." type="warning" %}

When you run the executable it will simply ask you for a directory to extract to. **DO NOT EXCTRACT TO YOUR DOWNLOADS FOLDER**, which will dump dozens of files. Instead provide a path next to the ruby installation such as `C:\RubyDevKit`.

You need to first initialize and then bind the devkit to your Ruby installation. Open a command prompt and navigate to the newly extracted folder:

```bash
cd C:\RubyDevKit
```

Then to initialize the devkit run:

```ruby
ruby dk.rb init
```

Finally install and bind the devkit to your ruby installation:

```ruby
ruby dk.rb install
```


## Install Bundler

Install bundler with:

```ruby
gem install bundler
```

Fairly straightforward.


## Install the Gems for a Github-pages project

As mentioned earlier, this guide assumes you have already created a github pages jekyll site that you've cloned on your windows machine and want to serve locally.
Your project should contain a [Gemfile](http://bundler.io/gemfile.html) that contains at least the following:

```ruby
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
gem 'wdm', '>= 0.1.0' if Gem.win_platform?
```

With a command prompt cd into the directory containing the `Gemfile` and use bundler to install the github-pages jekyll dependencies:

```ruby
bundle install
```

{% include alert.html message="If at this point you get a compiler error related to a json gem, that means your Ruby Development Kit was not installed correctly. 
<br />
Double check that they are both the latest version, both are 64 bit, and you ran the commands to init and install the `dk.rb` file" type="info"%}

<br />

# 2. Get **Jekyll Server** Working

This is where most tutorials leave off and where the majority of my frustration in getting Jekyll to serve on Windows 10 started.

At this point, the offical guide claims that you should be able to just run:

```ruby
jekyll serve
```

And that should work.

However on two separate Windows 10 Instances I have had problems here. Here is what I had to do:

## Fix SSL "certificate verify failed"

![](https://i.imgur.com/w8Rtpix.png)

Apparently this is a long-standing Issue with Ruby on Windows. You can read more about it here: [https://gist.github.com/fnichol/867550](https://gist.github.com/fnichol/867550)

It boils down to the OpenSSL library that Ruby uses not including SSL certificate authorities in a file so we will install it using an Environment Variable.

First, download the `cacert.pem` file from [http://curl.haxx.se/ca/cacert.pem](http://curl.haxx.se/ca/cacert.pem)

This file will need to be stored in a permanent location. Since we already have `C:\RubyDevKit`, I'd recommend creating a folder for it there, for example `C:\RubyDevKit\SSLCert`. Move `cacert.pem` to that folder.

![](https://i.imgur.com/uLEUJCx.png)

Now we need to set up an environment variable named `SSL_CERT_FILE`. To do so with [Eveditor](http://eveditor.com/), launch the program and click 'System variables' in the left sidebar and click the 'Run as administrator' button to restart the program with elevated priviledges.

Now click 'New' In the menu bar, then at the bottom enter `SSL_CERT_FILE` as the `Name` and the full path for the file for the `Value`, which in my case is `C:\RubyDevKit\SSLCert\cacert.pem`.

![](https://i.imgur.com/veaf67X.png)

Make sure to click the `Set` button, and you're good to go. Note that if you have an open command prompt it will need to be re-launched to load the new variable.

However keep your environment variable editor open, because there is one more thing you may need to set.


## Fix "No repo name found"

Now when you run `jekyll serve` you may see this error:

```
ERROR: YOUR SITE COULD NOT BE BUILT:
       ------------------------------------
       No repo name found. Specify using PAGES_REPO_NWO 
       environment variables, 'repository' in your 
       configuration, or set up an 'origin' git remote 
       pointing to your github.com repository.
```

It should be noted that my project's `.git/config` *does* have an 'origin' git remote pointing at my github.com repository, but for some reason that is not enough. While environment variables are fun, they are not portable, so I chose to modify my `_config.yml` file instead to define the `repository` variable.

Simply add a `repository` key with value `<USERNAME>/<REPOSTIROY_NAME>` on a new line anywhere in the `_config.yml` file, which in my case was:

```yml
repository:         'JohannesMP/com.johannesmp.blog'
```


## Fix "The GitHub API credentials you provided aren't valid."

When trying to run `jekyll serve` you may see one of these errors:

```bash
Error:  The GitHub API credentials you provided aren't valid.
```
```bash
GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
```

This can be due to having 2-factor authentication set up on github, or the credentials not stored otherwise. The easiest fix for this is to generate a unique `Personal Access token` through github's website, and store that in the `JEKYLL_GITHUB_TOKEN` environment variable.

To generate a new access token for Jekyll visit [https://github.com/settings/tokens](https://github.com/settings/tokens), then click 'Generate new token'.

For `Token description` enter something so you know what it is being used by. I named mine `JEKYLL_GITHUB_TOKEN_COMPUTERNAME`.

Under `Select scopes` simply check the `repo` checkbox.

When you click `Generate Token`, you will see the token at the top of the page (screenshot is of a dummy token of course):
![](https://i.imgur.com/AOdxamY.png) 
Make sure to either leave that open or copy and paste it in a new [notepad++](https://notepad-plus-plus.org/) tab, since you will never be able to see it again once you navigate away from that page.

Open your favorite environment variable editor and create a new System Variable. for `Name` put `JEKYLL_GITHUB_TOKEN` and for `Value` paste in the new token.

![](https://i.imgur.com/rliKTQb.png)

Make sure that you did not accidentally copy any spaces to either side of the token! Make sure to hit `Set` to save the token, and re-open your command prompt so that it reloads the env vars.


## Fix jekyll-remote-theme/libcurl dependency error
 
Lastly, you may get a dependency error that states: 

    Dependency Error: Yikes! It looks like you don't have jekyll-remote-theme or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is:  
        'Could not open library 'libcurl': The specified module could not be found. . Could not open library 'libcurl.dll': The specified module could not be found. . Could not open library 'libcurl.so.4': The specified module could not be found. . Could not open library 'libcurl.so.4.dll': The specified module could not be found.' 
        
    If you run into trouble, you can find helpful resources at https://jekyllrb.com/help/!

As of the time of this writing (January 2018), this will occur if you are using the github-pages gem on Windows, because v171 of the gem added the default theme `jekyll-remote-theme` as a dependency which in turn requires `libcurl`.

For more info please refer to the relevant issue on the github pages gem bug tracker: [https://github.com/github/pages-gem/issues/509](https://github.com/github/pages-gem/issues/509)

Hopefully future updates of this gem will remove the dependency, but for the time being you have two options:
- Install the libcurl dependency yourself.
- Modify your Gemfile to force version 170 which doesn't have this dependency.

For the time being I chose to do the latter, and so in my Gemfile I changed

    gem 'github-pages'

To
    
    gem 'github-pages', '170'



# Conclusion

And with that, `jekyll serve` should finally work as intended:

![](https://i.imgur.com/kX04XY8.png)

