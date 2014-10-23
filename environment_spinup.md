The Super-Simple Rails Environment Setup
========================================

The quickest and simplest way to get a Rails setup going, on a fresh Linux install.

I've tested this process with the following tools:

* Ubuntu 14.04.1 or Fedora 20 (as a base)
* `ruby-install` (for ruby installation)
* `chruby` (for ruby management)

Though the steps should roughly correlate on any Linux system. The packages might be slightly different versions, and the commands may be slightly different (eg. `yum` instead of `apt-get`), but the processes will be the same. And at the end we'll have a fully functional Rails development environment!

I'll assume your system is fully up to date, with all updates installed, but no other prerequirements. Ready? Here we go.

Special steps for Fedora
------------------------

A base Fedora install is a lot more bare-boned than Ubuntu, and as such requires some really basic packages installed before we can get going. Both `chruby` and `ruby-install` specify `wget` as part of their installation instructions, so we'll need to install that first.

```sudo yum install -y wget```


Setting up `ruby-install`
-------------------------

**What is it?**

A tool used to download the ruby source and associated dependencies and compile it all neatly for you to use.

**Why do we use it?**

Through a quirk of fate (or a disgruntled Debian package maintainer), the rubies available in the Ubuntu software repositories aren't quite up to scratch. At time of writing, they contain different packages for both ruby 1.9.3 (called all of `ruby`, `ruby1.9.3`, and `ruby1.9.1`, naturally) and ruby 2.0 (named `ruby2.0`), no ruby 2.1, and while they can all be installed on your system, you can't have multiple patch-level versions of ruby 1.9.3 (for example), or beta versions (such as ruby 2.0.0-rc2), or old non-supported rubies (such as 1.8.7).

And it's easy to use! One line in your terminal to install any version of ruby currently around. And keeping it separate from `chruby` means that we can update `ruby-install` as new versions of ruby are released (to get the new specs and dependencies, etc.) and doing so will have no other side effects, unlike various all-in-one tools around.

**Where is it?**

Here! [https://github.com/postmodern/ruby-install](https://github.com/postmodern/ruby-install)

**Installation**

The latest version of ruby-install is 0.4.3, and we can install it super-simply like so:

```
# Download the zipped source for ruby-install
wget -O ruby-install-0.4.3.tar.gz https://github.com/postmodern/ruby-install/archive/v0.4.3.tar.gz

# Unzip the source
tar -xzvf ruby-install-0.4.3.tar.gz

# Go into the directory we just unzipped
cd ruby-install-0.4.3/

# And install the software
sudo make install
```

Once it's installed, you can get a list of a bunch of available rubies to install using `ruby-install`. It's a long list, but it's not a complete list - it keeps all of the common versions on standby, but you can install any version you like just by specifying it.

Let's install one. The latest version (at writing) of MRI ruby is `2.1.3`, but it's not in the list. Never fear. It's smart enough to know what to do if you give it the name of a valid ruby version, so we can install it like so:

```
# Install the following named ruby, to the specified location
ruby-install ruby 2.1.3
```

It knows where to get the source code for ruby 2.1.3, and also what dependencies are needed to compile it. This is why it might ask you for your sudo password - it will use your package manager (`apt-get`, or `yum`) to go and get all the dependencies and install them (if you don't already have them). And for a basic Fedora install, that will likely be a very long list.

Once it's done all of that, it will start compiling ruby - lots and lots of random output as checks a lot of things and then compiles all of the files. There doesn't appear to be an in-built way to silence this output, but you can just let it do its thing - go get a can of Diet Coke or something, it will take a few minutes.

When it's done, you'll see a nice happy:

```
>>> Successfully installed ruby 2.1.3 into /home/<your username>/.rubies/ruby-2.1.3
```

Hooray! But how do we use it? We have no way of activating it yet, so that if we just call `ruby` it will pick up the version we just installed. Enter `chruby`.

Setting up `chruby`
-------------------

**What is it?**

`chruby` is a simple shell tool used to manage ruby installations on your system. It's main purpose is to switch between different versions you may have installed, and handle all of the shell mess for you, so for example, calling `ruby` or `rake` will always point to the correct version, even though you may have multiple.

As it's by the same author as `ruby-install`, the two tools work very well together.

**Why do we use it?**

There are tools already built into Linux to do things like this, such as `update-alternatives`, but they're simply not as convenient to use. They also don't have things like automatic ruby switching when you change directories in your terminal - one of my favorite features.

**Where is it?**

Right here: [https://github.com/postmodern/chruby](https://github.com/postmodern/chruby)

**Installation**

The latest version of chruby is 0.3.8, and we can install it super-quickly like so.

```
# Download the zipped source for chruby
wget -O chruby-0.3.8.tar.gz https://github.com/postmodern/chruby/archive/v0.3.8.tar.gz

# Unzip the source
tar -xzvf chruby-0.3.8.tar.gz

# Go into the directory we just unzipped
cd chruby-0.3.8/

# Install the software
sudo make install
```

Once it's installed, we can configure it.

```
# Add this to your ~/.bashrc file (if using bash) or ~/.zshrc (if using zsh)
source /usr/local/share/chruby/chruby.sh

# Add this as well if you want to use chruby's switching behaviour,
# ie. so you can change rubies automatically when cd-ing into certain directories
source /usr/local/share/chruby/auto.sh
```

We can now set the ruby we installed earlier as our default ruby, like so:

```
# Add this to your .bashrc or .zshrc file like before, after the lines that load chruby
chruby 2.1.3
```

To get it working, you'll need to restart your terminal (closing it and re-opening it is easiest.) If you run `chruby` you should now see `ruby-2.1.3` listed with a star next to it, meaning it is the currently active ruby.

If you had multiple rubies listed, you could switch between them using `chruby <ruby>`, eg. `chruby 2.1.0`. But for now we just have the one, and that's just fine.

Setting up Rails
----------------

So now you have a working ruby. What about Rails?

Ruby comes with its own package manager called `rubygems`, and this is what we can use to install Rails (gems are just little bundled-up packages of ruby code, and `rails` is simply just a gem.)

```
gem install rails
```

This will install the latest version of rails (4.1.6 at writing) and all of its dependencies, 23 in all. It'll take a while, as it figures out the dependencies, installs the gems, and then parses and installs documentation. (You can skip the documentation install if you really want, but hey you might want it one day!)

Check your rails installation by running `rails -v` - it should tell you that it's using 4.1.6. Hooray!

Now is when you can get out your #{rails\_tutorial\_of\_choice} and start following it. If you haven't got one already, I highly recommend the following book:

**Rails 4 in Action** - [http://www.manning.com/bigg2/](http://www.manning.com/bigg2/)

However if you just want to test spinning up a brand new Rails app to test it out, you can do it like so:

```
rails new test_app
```

This will create a new app in your `test_app` folder. It will copy over all the files for a skeleton application, then run `bundle install` - `bundler` is Yet Another Dependency Management System, this one specifically for all the different gems you might want to use in your Rails app.

`Bundler` will make sure your app is always using versions of gems that are all compatible with each other - if the latest version of Gem A you want to use depends the gem 'froofroo' version 1.2, and the latest version of Gem B depends on the gem 'froofroo' version 1.3, it will automatically get versions of Gem A and Gem B that rely on the same version of 'froofroo'. Sometimes it's not perfect, but it's a damn sight better than managing dependency versions yourself. `Bundler` is a very neat tool.

**OhNoes #1**

At this point `bundler` will most likely error out with a big long error about sqlite3.h not being found. Welcome to Rails. By default, Rails will try to set up new apps connecting to the `sqlite3` database library - because we don't have the development libraries for sqlite installed, it won't be able to create the app. Fix that with:

```
# For Ubuntu
sudo apt-get install libsqlite3-dev

# For Fedora
sudo yum install -y sqlite-devel
```

> If you wanted to use a different database instead (eg. PostgreSQL or MySQL) you would install the development libraries for those databases instead (`libpq-dev` or `libmysqlclient-dev`) and then generate your rails app with either `-d postgresql` or `-d mysql` to generate default database configs for those databases. But for beginning, sqlite is sufficient.

And then re-run the `rails new` command. Overwrite files where necessary. Now you have a Rails app!

**OhNoes #2**

To test your rails app, `cd` into your `test_app` folder and run `rails server` (or `rails s` for short). You'll get another big fat error - this one about a missing JavaScript runtime.

I wish some of these errors would be fixed up when setting up new Rails apps. I really do. But for now we just have to work with them.

To cut a long story short, way back in Rails 3.1 they introduced a new feature called the _asset pipeline_. For a long time it was misunderstood, everyone thought it sucked, but now people have a hard time remembering what life was like without it. (Spoilers: It was significantly less awesome.)

(For more on the asset pipeline, check out this great guide: http://guides.rubyonrails.org/asset_pipeline.html)

The error message will give you a URL to visit to see a list of possible runtimes, you need to install one of them. The one I always pick is node.js - it's easy to install (`sudo apt-get install nodejs` or `sudo yum install -y nodejs`) and you only need to do it once, ever. 

Alternatively, if you don't want to install a package and instead want a pure Ruby solution, you can use the gem `therubyracer`. You can put it in your Gemfile like `gem 'therubyracer'`, and then `bundle install` to get it installed.

So pick your favorite solution, and install it. Once you have one, you can re-run `rails server`.

**Great Success!!**

Once WEBrick (the inbuilt ruby server) has loaded (it will tell you it has loaded on port 3000), you can then open your browser and visit the following URL:

```
http://localhost:3000
```

If you see the following message **Welcome aboard! You're riding Ruby on Rails!** then my job here is done :)
