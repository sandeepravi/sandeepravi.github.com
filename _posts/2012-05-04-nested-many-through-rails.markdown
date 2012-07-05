---
layout: post
---

Nested has many through in Rails 3.1
====================================

<p class="date">04 May 2012 - Bangalore, India</p>

There’s a cool feature implemented in Rails 3.1. You can now have a **nested “has many through” relationship**. Here’s an example to show what I mean by a “nested hmt”:

<!-- more start -->

    class User < ActiveRecord::Base
      has_many :memberships
      has_many :organizations, :through => :memberships
      has_many :deals, :through => :organizations # => This is not possible in Rails < 3.1

     def has_deals?
       self.deals.any?
     end
    end

As you can see, *organizations* table here acts as the join table between *users* and *deals*, although *users* has a has many relationship with *organizations* through *memberships*. This nifty feature is very useful when dealing with complex relationships and you don’t have to simulate the “has many through” here.

<!-- more end -->
