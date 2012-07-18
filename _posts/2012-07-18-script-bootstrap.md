---
layout: post
title: Bootstrapping a Rails application
---

script/bootstrap
====================

<p class="date">18 Jul 2012 - Bangalore, India</p>

I have spent t lot of time setting up an application, doing the mundane
"rake db:create", "rake db:migrate", "rake db:seed", "bundle" and a
variety of other dumb things for every application. This is, honestly,
quite annoying especially when I start working on a project midway.

It's also important to make an application *"noob-proof"* so that anyone
can setup the application without any difficulty. This is particularly
useful when you have a separate team doing Front End HTML and they don't
know about RoR.

<!-- more start -->

Due to all this, I have started including a bootstrap file which sets up
the application. Here's what I generally do in my bootstrap file.

* Database Creation
* Database Migration
* Database Seed
* Bundle gems
* Dependency Checks
* Suggest what needs to be done if a dependency fails

### Caching bootstrap results

Now that our bootstrap file does all this, it's important that
all these checks are not done every single time. To improve this, I
started caching the results and performing these actions only if the
associated files have been modified. This makes sure that the bootstrap is quick and effective.

Caching is actually pretty simple. We just store the results of the
query in a cache file in the tmp dir. Here's how I implemented the cache
method in my bootstrap script.

    # Public: Method to cache results in a file
    #
    # key - A String with the result we are trying to cache
    #
    # Returns the content of the cached file
    def check(key)
      cache_file = "tmp/.#{key}"
      if File.exists?(cache_file)
        old_version = File.read(cache_file)
      else
        old_version = nil
      end

      version = yield(old_version)
      File.open(cache_file, 'w') { |f| f.puts(version || 'cached') }
    end

I also add "rails s" to the script as an option so that the script
starts the server as well!

Now, all I (or anyone else) need to do is

    script/bootstrap

<!-- more end -->
