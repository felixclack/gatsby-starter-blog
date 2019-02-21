---
title: How should I use ActiveRecord Callbacks?
date: "2014-06-26T23:46:37.121Z"
---

ActiveRecord callbacks... you've probably used them a lot.

Being able to hook into the lifecycle of an object is a powerful feature of Rails and it can give you great gains in productivity early on while you're building your product.

## What happens when the lifecycle of an object gets too tightly coupled with the side-effects?

The danger of this power, like any superpower, is the potential for abuse and what it will do to your objects.

Over time the coupling grows and before you know it, the side effects of creating an instance of one of your objects prevent you from safely instantiating that instance outside the user interface.

Maybe that's not a problem for you, but I suspect for a lot of people it is. Sending emails is a classic.

Have you ever sent an accidental email to every customer while migrating your data because of an inerrant callback?

    ---
    Hi,

    your account has been charged $29.

    Have a good day.
    ---

Aargh. Cue a whole bunch of concerned customers hitting up your support channels asking what happened.

Or how about testing those little buggers? Those side effects are, at the very least, slowing down your test suite, and at the worst, causing failures. So you end up monkey patching ActiveRecord in order to prevent the callbacks from firing in your test environment.

Is this a code smell?

Callbacks themselves aren't bad, it's the way we (ab)use them that is the problem.

Most, perhaps all, side effects shouldn't be triggered by an ActiveRecord callback. Reserve this precious resource for essential actions that directly affect the object itself.

Email is an example of a side effect that doesn't belong there.

Sometimes you may need to create other objects as part of the setup process. Again, this is probably a bad idea. Tying the creation of 2 objects so closely together is asking for trouble the moment you need to create one of those objects independent of the other.

    class User
      after_create :create_account

      private

      def create_account
        accounts.create
      end
    end

What happens when I want to create a `User` without an `Account`. I can't. This kind of dependency is better created outside the model.

## So what are they good for?

I find it useful to stick to this simple rule. Only use callbacks on the object they are called on, eg. for a `User` model, the callbacks should only be able to perform commands on the `User` instance.

`before_save` & `before_create` can be good for guaranteeing default values of your objects. If it's a constant value, then this is not the right place, that's definitely a concern better handled by your database.

Calculated values are a different matter. What if you need to create a unique token for each user?

    class User
      before_create :set_token

      private

      def set_token
        while User.not.exists?(token: (token = SecureRandom.base64))
          self.token = token
        end
      end

This is where the power of ActiveRecord callbacks shine.

## What about the other code that was in my callbacks?

Now that you're only allowing callbacks to perform commands on the object they're called on, what do you with the other commands?

I usually approach this in 2 steps. The first step is to identify at what point in the application I need to trigger the callback, and secondly, determine if it is needed in multiple places.

Where code relates to setting up associations on an object the first time it is created, it's usually safe to move this code into the related `controller#action`.

Let's take sending an email as a classic example.

    class UsersController
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


<!--End mc_embed_signup-->
