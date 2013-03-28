The Super-Simple Rails Environment Setup
========================================

The quickest and simplest way to get a Rails setup going, on a brand new Ubuntu install.

I'll be using the following tools:

* Ubuntu 12.10 (as a base)
* `chruby` (for ruby management)
* `ruby-build` (for ruby installation)

Though the steps should roughly correlate on any Ubuntu system. The packages might be slightly different versions, but the processes will be the same. And at the end we'll have a fully functional Rails development environment!

I'll assume your system is fully up to date, with all updates installed. Ready? Here we go.

Setting up `chruby`
-------------------

**What is it?**

`chruby` is a simple shell tool used to manage ruby installations on your system. It's main purpose is to switch between different versions you may have installed, and handle all of the shell mess for you, so for example, calling `ruby` or `rake` will always point to the correct version, even though you may have multiple installed.

**Why do we use it?**

Through a quirk of fate (or a disgruntled Debian package maintainer), the rubies available in the Ubuntu software repositories aren't quite up to scratch. At time of writing (13/02/2013), they contain different packages for both ruby 1.8.7 (called `ruby` and `ruby1.8`) and ruby 1.9.3 (named `ruby1.9.1`, of course) and while they both can be installed on your system, you can't have multiple versions of ruby 1.9.3 (for example), or beta versions (such as ruby 2.0.0-rc2), and you have to remember to call the right command for the version of ruby you're using (eg. `ruby` vs `ruby19`).

**Where is it?**

Right here: [https://github.com/postmodern/chruby](https://github.com/postmodern/chruby)

**Installation**

The latest version of chruby is 0.3.2, and we can install it super-quickly like so.

```
# Download the zipped source for chruby
wget -O chruby-0.3.2.tar.gz https://github.com/postmodern/chruby/archive/v0.3.2.tar.gz

# Unzip the source
tar -xzvf chruby-0.3.2.tar.gz

# Go into the directory we just unzipped
cd chruby-0.3.2/

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

Restart your terminal window (closing and reopening it is easiest) and `chruby` will be ready to go!

**Super simple setup**

At the moment, you have no rubies installed so `chruby` doesn't do a lot. However, it provides a simple setup script - `sudo ./scripts/setup.sh` - that will automatically install the dependencies for, and then install the latest versions of:

* **ruby** ('normal' ruby, also known as _MRI_ or _cruby_, a ruby interpreter written in C)
* **jruby** (a ruby interpreter written in Java), and
* **rubinius** (also known as _rbx_, a ruby interpretator written in ruby!)

If you don't want to do this you will need to install the dependencies anyway before you move onto installing any rubies using `ruby-build`. These base dependencies are:

```
# For MRI ruby
sudo apt-get install build-essential zlib1g-dev libyaml-dev \
libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev

# For jruby
sudo apt-get install openjdk-7-jdk

# For rubinius
sudo apt-get install gcc g++ automake flex bison ruby-dev rake \
zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libreadline-dev libncurses5-dev
```

Once the right dependencies are installed for the version of ruby you want to install, you can move onto setting up `ruby-build`.

Setting up `ruby-build`
-----------------------

**What is it?**

A tool used to download the ruby source and associated dependencies and compile it all neatly for you to use.

**Why do we use it?**

It's easy! One line to install any version of ruby currently around and supported. And keeping it separate from `chruby` means that we can update `ruby-build` as new versions of ruby are released (to get the new specs and dependencies, etc.) and doing so will have no other side effects, unlike various all-in-one tools around.

**Where is it?**

Here! [https://github.com/sstephenson/ruby-build](https://github.com/sstephenson/ruby-build)

**Installation**

`ruby-build` requires git to clone the repository and install, which you can install (if you haven't already) using:

```
sudo apt-get install git
```

Once git is installed, run the following to install `ruby-build`:

```
# Clone and download the ruby-build source code
git clone git://github.com/sstephenson/ruby-build.git

# Move into the directory we downloaded it into
cd ruby-build

# Install the software
sudo ./install.sh
```

Once it's installed, you can get a list of all available rubies to install using `ruby-build --definitions` - as you can see, there's a *lot* of them.

Let's install one. The latest version (at writing) of MRI ruby is `1.9.3-p385`, which is in the list. `chruby` will automatically read any rubies that are installed in `~/.rubies` so we can use `ruby-build` to install a ruby like so:

```
# Install the following named ruby, to the specified location
ruby-build 1.9.3-p385 ~/.rubies/1.9.3-p385
```

It will go off and download ruby's dependencies (in this case, just yaml) and install it for you. It may take a while, up to five or so minutes depending on your machine.

Once it's done, you can set your new ruby as your default ruby like so:

```
# Add this to your .bashrc or .zshrc file like before
chruby 1.9.3-p385
```

And restart your terminal. To check if ruby is being detected, run `ruby -v` - it should tell you it's using the version we set. Great success!

Setting up Rails
----------------

So now you have a working ruby. What about Rails?

Ruby comes with its own package manager called `rubygems`, and this is what we can use to install Rails (gems are just little bundled-up packages of ruby code, and `rails` is simply just a gem.)

```
gem install rails
```

This will install the latest version of rails (3.2.12 at writing) and all of its dependencies, 28 in all. It'll take a while, as it figures out the dependencies, installs the gems, installs `ri` documentation, and then installs `rdoc` documentation. (You can skip the documentation install if you really want, but hey you might want it one day!)

Check your rails installation by running `rails -v` - it should tell you that it's using 3.2.12. Hooray!

Now is when you can get out your #{rails\_tutorial\_of\_choice} and start following it. If you haven't got one already, I highly recommend the following book:

**Rails 3 in Action** - [http://www.manning.com/katz/](http://www.manning.com/katz/)

However if you just want to test spinning up a brand new Rails app to test it out, you can do it like so:

```
rails new test_app
```

This will create a new app in your `test_app` folder. It will copy over all the files for a skeleton application, then run `bundle install` - `bundler` is Yet Another Dependency Management System, this one specifically for all the different gems you might want to use in your Rails app.

`Bundler` will make sure your app is always using versions of gems that are all compatible with each other - if the latest version of Gem A you want to use depends 'froofroo' version 1.2, and the latest version of Gem B depends on 'froofroo' version 1.3, it will automatically get versions of Gem A and Gem B that rely on the same version of 'froofroo'. `Bundler` is a very neat tool.

**OhNoes #1**

At this point `bundler` will most likely error out with a big long error about sqlite3.h not being found. Welcome to Rails. By default, Rails will try to set up new apps connecting to the `sqlite3` database library - because we don't have the development libraries for sqlite installed, it won't be able to create the app. Fix that with:

```
sudo apt-get install libsqlite3-dev
```

_Quick Tip:_ If you wanted to use a different database instead (eg. PostgreSQL or MySQL) you would install the development libraries for those databases instead (`libpq-dev` or `libmysqlclient-dev`) and then generate your rails app with either `-d postgresql` or `-d mysql` to generate default database configs for those databases. But for beginning, sqlite is sufficient.

And then re-run the `rails new` command. Overwrite files where necessary. Now you have a Rails app!

**OhNoes #2**

To test your rails app, `cd` into your `test_app` folder and run `rails server` (or `rails s` for short). You'll get another big fat error - this one about a missing JavaScript runtime.

I wish some of these errors would be fixed up when setting up new Rails apps. I really do. But for now we just have to work with them.

To cut a long story short, Rails 3.1 introduced a new feature called the _asset pipeline_. If you've done any reading about Rails, you've probably heard about how much the asset pipeline sucks for a multitude of reasons. I personally am a big fan of it, but it could be improved in some parts - like here.

The error message will give you a URL to visit to see a list of possible runtimes, you need to install one of them. The one I always pick is node.js - it's easy to install (`sudo apt-get install nodejs`) and you only need to do it once, ever. So install it, and then run `rails server` again.

**Great Success!!**

Once WEBrick (the inbuilt ruby server) has loaded (it will tell you it has loaded on port 3000), you can then open your browser and visit the following URL:

```
http://localhost:3000
```

If you see the following message **Welcome aboard! You're riding Ruby on Rails!** then my job here is done :)
