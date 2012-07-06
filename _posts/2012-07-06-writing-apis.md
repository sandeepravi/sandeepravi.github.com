---
layout: post
title: Writing APIs in Rails
---

Writing APIs in RoR
====================

<p class="date">06 Jul 2012 - Bangalore, India</p>

I have seen and written a lot of code, especially related to APIs, which, now, I feel is quite
objectionable. Designing APIs so that someone else can use
it can be quite irritating to say the least as you need to deal
with various other teams - iOS / Android / Blackberry / whatever.

When you are working on an application that just serves APIs, things can
be easier as, well, you just have to deal with your json (hopefully!)
responses. But as soon as you throw in a web component things can get
messy really quickly.

Here are a few points which I try to keep in mind when I design an
application which serves an API and has a web component as well. All
this is assuming that you do not use MVC in views to consume APIs. Although I
don't have an issue with doing that, unless your application is huge,
there is crazy AJAX and MVC is actually needed in your front end, this
is just over-engineering. Clean, managable, extendable code can be
written by having web and api logic the same application too:

<!-- more start -->

### 1. Rules

It's very important to have a specific way to do things so that
consistency is maintained everywhere. When you don't have rules on such
applications, it is very easy to lose yourself in the code and you end
becoming a prisoner of your code instead of otherwise.

Rules can and should be set in almost everything; responses, requests,
how logic should be handled in models. Here's something that can be
done. Instead of passing all parameters explicitly, a Hash can be passed
with a generic rule in the params

*Instead of:*

    def comment(post_id, comment, type)
      call_method
    end

*You can do:*

    def comment(params, options = {})
      call_method
    end

Ofcourse, it can be argued here that you will not know the parameters
that need to be sent and the parameters are not clear, etc. and that
brings me to my next point.

### 2. Documentation

Documeting code is something where I have seen people having quite "extreme"
views. But making sure that all the methods are documented makes a lot
of things very easy and definitely saves a lot of frustration in the
long run. If you are not into documenting code, make sure that you have
a document with the API list and the reponse & request that needs to be
sent for each API. I use Jekyll (*will be posting a blog about this soon!*) for this.

I generally follow **_Tomdoc_** in my code as it's readable and can be parsed
as well.

    # This method comments on a Post
    #
    # params - A Hash containing the following keys:
    #          user_id: A String with the user id who commented
    #          comment: A String with the comment text
    #
    # Returns the Comment Object
    def comment(params)
      call_method
    end

Also, make sure that the documentation is updated as and when code is
written otherwise it's almost impossible to write all the API
documentations together.

### 3. Having a consistent structure for all responses.

This is the primary issue which can cause your code to get
unmanagable. Make sure that all your APIs return a consistent response.
When I checked the code that we had written, a lot of the API's
responses were not consistent. Some APIs returned a Hash, some an Array
and even the hashes that were returned did not have a structure to them.

    {posts: [{post: {id: 1}}, {post: {id: 2}}]} // fine
    {posts: [{id: 1}, {id: 2}]} // fine
    [{id: 1}, {id: 2}] // not good

Here, the first 2 responses are quite different yet both make sense in
their own way. I don't think that either one is "better" over the other
but <b>make sure that one of the first two is picked as a standard reponse
structure</b>.

When looking for flexibility, Hash is definitely the way to do as it's
very easier to extend the functionality in the future.

### 4. API namespace

This is again something that many people may not agree but I feel the
code is definitely cleaner this way.

Having a separate namespace for all APIs and handling all API related
reponses in the namespace makes sure that your web logic does not get entangled
with API logic in the controller. There are times when we end up writing
an "if" condition just for a specific case which is there in an API.
Although it is fine to have it one such place, this never happens. Once
an if condition is accepted, this will become the norm.

### 5. Using templates

to_json is a bad idea. Although, ideally, I feel the JSON responses are
best in a Presenter, sometimes Presenters are just not necessary. In
such cases, rabl / json builder is a good tool to use. rabl is good if
you need DSLy syntax but you may end up spending a lot of time over
customization so jsonbuilder is a good fit.

    def show
      @user = User.find(params[:id])
      respond_to do |format|
        format.json {@user.to_json}
      end
    end

In users_controller.rb:
    def show
      @user = User.find(params[:id])
    end

In show.json.rabl:
    object @user

The second option here is a lot cleaner and also abstracts all the
responses into rabl and make sure that the controller is clean.

### 6. Testing Controllers

There is a consensus that controller code doesn't need to be tested as
there is no business logic there, its useless etc. As something who
follows TDD, I totally disagree with that (maybe another blog post on that later?); It is
extremely important in applications which expose APIs that they have proper
test cases as this makes sure that all the responses are tested (at the
least).

Frankly, almost all of these tips can be followed to have clean code in any
Rails application, but it's particularly useful to follow these while writing
APIs.

After following the above Rules, I am able to write much more
maintainable code and I hope this helps anyone who is planning to write
their next public API.

Feel free to add a comment if you disagree with any of these or if you
have any other tip to add.

<!-- more end -->
