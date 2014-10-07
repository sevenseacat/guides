The Super-Simple Rails Environment Setup
========================================

The quickest and simplest way to get a Rails setup going, on a brand new Ubuntu install.

I'll be using the following tools:

* Ubuntu 14.04.1 (as a base)
* `chruby` (for ruby management)
* `ruby-install` (for ruby installation)

Though the steps should roughly correlate on any Ubuntu system. The packages might be slightly different versions, but the processes will be the same. And at the end we'll have a fully functional Rails development environment!

I'll assume your system is fully up to date, with all updates installed, but no other prerequirements. Ready? Here we go.

Setting up `chruby`
-------------------

**What is it?**

`chruby` is a simple shell tool used to manage ruby installations on your system. It's main purpose is to switch between different versions you may have installed, and handle all of the shell mess for you, so for example, calling `ruby` or `rake` will always point to the correct version, even though you may have multiple installed.

**Why do we use it?**

Through a quirk of fate (or a disgruntled Debian package maintainer), the rubies available in the Ubuntu software repositories aren't quite up to scratch. At time of writing, they contain different packages for both ruby 1.9.3 (called all of `ruby`, `ruby1.9.3`, and `ruby1.9.1`, of course) and ruby 2.0 (named `ruby2.0`), no ruby 2.1, and while they both can be installed on your system, you can't have multiple versions of ruby 1.9.3 (for example), or beta versions (such as ruby 2.0.0-rc2), and you have to remember to call the right command for the version of ruby you're using (eg. `ruby` vs `ruby19`).

(Yes I know about `update-alternatives`, but that's a pain, and doesn't fix the entire problem.)

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

Restart your terminal window (closing and reopening it is easiest) and `chruby` will be ready to go!

**Super simple setup**

At the moment, you have no rubies installed so `chruby` doesn't do a lot. However, it provides a simple setup script - `sudo ./scripts/setup.sh` - that will automatically install the dependencies for, and then install the latest versions of:

* **ruby** ('normal' ruby, also known as _MRI_ or _cruby_, a ruby interpreter written in C)
* **jruby** (a ruby interpreter written in Java), and
* **rubinius** (also known as _rbx_, a ruby interpretator written in ruby!)

This is completely optional - if you don't want to do this and just want to install your own rubies, skip ahead to the next section.

Setting up `ruby-install`
-------------------------

**What is it?**

A tool used to download the ruby source and associated dependencies and compile it all neatly for you to use.

**Why do we use it?**

It's easy! One line to install any version of ruby currently around and supported. And keeping it separate from `chruby` means that we can update `ruby-install` as new versions of ruby are released (to get the new specs and dependencies, etc.) and doing so will have no other side effects, unlike various all-in-one tools around.

**Where is it?**

Here! [https://github.com/postmodern/ruby-install](https://github.com/postmodern/ruby-install)

(Yep, it's by the same author. He's one smart cookie.)

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

Let's install one. The latest version (at writing) of MRI ruby is `2.1.3`, but it's not in the list. Never fear. `chruby` will automatically read any rubies that are installed in `~/.rubies` so we can use `ruby-install` to install a ruby like so:

```
# Install the following named ruby, to the specified location
ruby-install ruby 2.1.3
```

It knows where to get the source code for Ruby 2.1.3, and also what dependencies are needed to compile it. This is why it might ask you for your sudo password - it will use your package manager to go and get all the dependencies and install them (if you don't already have them).

Once it's done all of that, it will start compiling ruby - lots and lots of random output as checks a lot of things and then compiles. There doesn't appear to be an in-built way to silence this output, but you can just let it do its thing - go get a can of Diet Coke or something, it will take a few minutes.

When it's done, you'll see a nice happy:

```
>>> Successfully installed ruby 2.1.3 into /home/becky/.rubies/ruby-2.1.3
```

Hooray! You can set your new ruby as your default ruby like so:

```
# Add this to your .bashrc or .zshrc file like before
chruby 2.1.3
```

And restart your terminal. To check if ruby is being detected, run `ruby -v` - it should tell you it's using the version we set. Great success!

Setting up Rails
----------------

So now you have a working ruby. What about Rails?

Ruby comes with its own package manager called `rubygems`, and this is what we can use to install Rails (gems are just little bundled-up packages of ruby code, and `rails` is simply just a gem.)

```
gem install rails
```

This will install the latest version of rails (4.1.6 at writing) and all of its dependencies, 26 in all. It'll take a while, as it figures out the dependencies, installs the gems, and then parses and installs documentation. (You can skip the documentation install if you really want, but hey you might want it one day!)

Check your rails installation by running `rails -v` - it should tell you that it's using 4.1.6. Hooray!

Now is when you can get out your #{rails\_tutorial\_of\_choice} and start following it. If you haven't got one already, I highly recommend the following book:

**Rails 4 in Action** - [http://www.manning.com/bigg2/](http://www.manning.com/bigg2/)

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

> If you wanted to use a different database instead (eg. PostgreSQL or MySQL) you would install the development libraries for those databases instead (`libpq-dev` or `libmysqlclient-dev`) and then generate your rails app with either `-d postgresql` or `-d mysql` to generate default database configs for those databases. But for beginning, sqlite is sufficient.

And then re-run the `rails new` command. Overwrite files where necessary. Now you have a Rails app!

**OhNoes #2**

To test your rails app, `cd` into your `test_app` folder and run `rails server` (or `rails s` for short). You'll get another big fat error - this one about a missing JavaScript runtime.

I wish some of these errors would be fixed up when setting up new Rails apps. I really do. But for now we just have to work with them.

To cut a long story short, way back in Rails 3.1 they introduced a new feature called the _asset pipeline_. For a long time it was misunderstood, everyone thought it sucked, but now people have a hard time remembering what life was like without it. (Spoilers: It was significantly less awesome.)

The error message will give you a URL to visit to see a list of possible runtimes, you need to install one of them. The one I always pick is node.js - it's easy to install (`sudo apt-get install nodejs`) and you only need to do it once, ever. So install it, and then run `rails server` again.

**Great Success!!**

Once WEBrick (the inbuilt ruby server) has loaded (it will tell you it has loaded on port 3000), you can then open your browser and visit the following URL:

```
http://localhost:3000
```

If you see the following message **Welcome aboard! You're riding Ruby on Rails!** then my job here is done :)
