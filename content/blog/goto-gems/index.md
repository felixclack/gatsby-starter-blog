---
title: What are my goto set of gems?
date: "2014-07-01T22:40:32.169Z"
---

You've started using Rails and have discovered the power of including gems into your project. Now you wonder if there is a set of default gems that would help kickstart your next rails projects. Are there some things you do every time?

For me, I find it useful to have a standard template that sets up the common stuff I use.

Let's start with the Gemfile:

    source 'https://rubygems.org'

    gem 'rails'

    gem 'bower-rails'
    gem 'coffee-rails'
    gem 'devise'
    gem 'foundation-rails'
    gem 'librato'
    gem 'sass-rails'
    gem 'slim-rails'

    group :development do
      gem 'better_errors'
      gem 'binding_of_caller'
      gem 'heroku'
      gem 'parity'
      gem 'pry'
    end

    group :test do
      gem 'capybara-webkit'
      gem 'rspec-rails'
    end

### gem 'rails'

Pretty obvious right? Well, I usually will loosely tie this to a version after I've run `bundle install` for the first time. So that line ends up looking something like `gem 'rails', '~> 4.1.2'`.

### gem 'better_errors'

The default error message template in Rails improved a lot with the 4.1 release, however I find `better_errors` to be so much better.

When combined with `gem 'binding_of_caller'` things get really powerful.

As well as giving a nicer looking stack trace to point you towards the error, you also get a live console bound to the application as it was when the error was raised. This makes debugging so much nicer.

### gem 'bower-rails'

So often I see projects where dependency management has been thought about when it comes to Ruby but the JavaScript looks like the Wild West, with random libraries dumped into v`endor/assets/javascripts`.

Your Javascript deserves as much care and attention as your Ruby and this is where `bower-rails` comes in.

Bower is dependency management for Javascript.

This gem makes it easy to integrate it with your Rails app.

Just run `rake bower:install`.

Hopefully as part of your [bootstrap script](http://felixclack.com/post/75161411572/onboarding-developers-to-your-rails-app-sucks).

This will create a `Bowerfile` for you, which is a place to specify your dependencies in a format similar to your gems.

It will look something like this...

    asset 'backbone'
    asset 'moment'

Every time you make a change to your `Bowerfile`, you need to run `rake bower:install` to update the dependencies.

### coffee-rails

If you ran the Rails generator then the default `Gemfile` will include this.

It allows you to write your Javascript in Coffeescript instead and compile it (at runtime in development or as part of your deployment process in production).

If you're using SASS and a templating language like HAML or Slim, then it makes sense to me to continue the whitespace significance in your Javascript.

Not familiar with Coffeescript? It's the default flavour of Javascript in Rails and is a ... that compiles into regular Javascript. Gives us some niceties like, ditching `;`, `{}`, `function`, `class` to make dealing with prototypes simpler, and of course whitespace significance.

Just append your js files with `.js.coffee` to trigger the compilation in your asset pipeline.

### devise

This gem has emerged as the most used authentication solution in the Ruby community. Packaged as a Rails engine, it gives you a standard set of user sign in/out tools that are easily customisable.

You will save yourself a lot of time by using this.

At it's most basic, you can have a user sign up/in system in less than 5 minutes:

    bin/rails g devise:install
    bin/rails g devise User
    bin/rake db:migrate

Then add `before_action :authenticate_user!` to the controllers you want to protect and you're done.

### foundation-rails

It's good to have a framework for your HTML and CSS as well as your server side code. [Foundation](http://foundation.zurb.com) is a front-end framework from the team at Zurb and is a great alternative to Twitter Bootstrap.

This gem will insert the necessary files into your asset pipeline and get you up and with a CSS framework quickly.

### heroku

The majority of apps I build initially get deployed to Heroku. It's such an easy way to deliver your app.

The `heroku` gem gives command line access to Heroku which you can use to create new apps and manage existing ones, including things like provisioning add ons, setting environment variables and managing your databases.

It should save you a few trips to the Heroku dashboard.

### librato

Librato is an excellent service to gather stats on the performance of your app on Heroku.

By default these include measuring the performance of your dynos, the router and Postgres DB, but by following their convention for outputting data in your log files you can easily measure so much more.

Librato has a few keywords you can use to help track things, and probably the 2 most common ones are `count` and `measure`. Simply prefix whatever you want to track with these keywords and it gets picked up ready to be analysed.

eg.

    count#user.clicks=1
    measure#database.query=200ms

`count` will just sum the values you give it, while `measure` gives you more statistical analysis options, such as the median, mean, standard deviation and much more.

### sass-rails

Another of the Rails default gems. It enables `SASS` in the asset pipeline.

[SASS](http://sass-lang.com) is a pre-processor for CSS that introduces some useful features, such as nested rules for easier to read code, mixins that allow you to re-use common rules across your stylesheets without having to duplicate code, variables and basic data structures such as a map.

This is hugely useful in making your CSS more manageable and readable.

### slim-rails

When it comes to writing my HTML templates I prefer to use a templating language with white space significance, most recently this has been [Slim](https://github.com/slim-template/slim).

For me, this reads so much better than HTML, however YMMV with this as it's quite a divisive topic.

### parity

A simple gem that will save you a lot of typing. This allows you to shorten your Heroku commands down to `staging migrate` instead of `heroku run rake db:migrate -a my-heroku-app-name`.

### pry

Essential piece of the toolkit. An `irb` session without `pry` feels clunky now.

`pry` is the missing REPL for Ruby. You want to deep dive into your code during a live IRB session, then this is for you. What about simply checking the source of a method before you run it? Yeah, you can do that too.

### rspec-rails

Writing tests first is a big deal for me, and `rspec` is my preferred tool.

This gem packages up all the pieces of Rspec that you'll need to get started with.

### capybara-webkit

I like to run some tests in a browser like environment, `capybara-webkit` does the job by giving you a headless version of webkit to run your tests in.

## Summary

So there it is, my default `Gemfile`. I hope it's useful to you and perhaps uncovers some useful tools for your next project.

<div id="mc_embed_signup">
<form action="http://felixclack.us6.list-manage1.com/subscribe/post?u=433936de0e6bdcd7769bd1b30&id=5151d967c3" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank" novalidate>
  <h2>Get more articles like this about using Rails Like A Pro</h2>
<div class="mc-field-group">
  <label for="mce-EMAIL">Email Address </label>
  <input type="email" value="" name="EMAIL" class="required email" id="mce-EMAIL"></div>
<div class="mc-field-group">
  <label for="mce-FNAME">First Name </label>
  <input type="text" value="" name="FNAME" class="" id="mce-FNAME"></div>
  <div id="mce-responses" class="clear">
    <div class="response" id="mce-error-response" style="display:none"></div>
    <div class="response" id="mce-success-response" style="display:none"></div>
  </div>    <!-- real people should not fill this in and expect good things - do not remove this or risk form bot signups-->
    <div style="position: absolute; left: -5000px;"><input type="text" name="b_433936de0e6bdcd7769bd1b30_5151d967c3" value=""></div>
  <div class="clear"><input type="submit" value="Get more from Rails Like A Pro" name="subscribe" id="mc-embedded-subscribe" class="button"></div>
</form>
</div>

<!--End mc_embed_signup-->
