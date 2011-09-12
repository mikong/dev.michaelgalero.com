--- 
wordpress_id: 21
layout: post
title: Basic User Signup with MerbAuth
tags: []

categories: 
- slug: rails
  title: Rails
- slug: ruby
  title: Ruby
- slug: merb
  title: Merb
wordpress_url: http://devblog.michaelgalero.com/2008/10/27/basic-user-signup-with-merbauth/
---
It's amazing how fast [Merb](http://merbivore.com/) has changed since I've dabbled with it early last month. I used the merb-auth plugin then and it was pretty ok, being familiar with the restful authentication plugin for Rails. But the merb-auth plugin is now obsolete, after only being introduced last June.

### Enter MerbAuth

Daniel Neighman (aka hassox) gave a talk during [MerbCamp](http://merbcamp.com/) on MerbAuth and MerbSlices and you can [download his slides from his github account](http://github.com/hassox/merb-camp-talks/tree/master/Presentations). There's also a recipe in the Merb Wiki cookbook for an [Authenticated Hello World](http://wiki.merbivore.com/cookbook/authenticated_hello_world) using MerbAuth, so be sure to check those out before following the tutorial below.

### MerbAuth Setup

As detailed in the [Authenticated Hello World](http://wiki.merbivore.com/cookbook/authenticated_hello_world) tutorial, MerbAuth is already in your Merb stack and you can use it right after:

1. generating your merb app
2. setting up your database, and
3. creating a hello world controller

So you just add the authentication in your router or your controller and you're good to go.

### Basic User Signup

This tutorial will show how to add validations to your User model, prepare your Signup page, and setup your Users controller. There are comments along the way that explain basic Merb stuff to someone with a Rails background. It assumes your Merb app uses Datamapper for its ORM and ERb for its templating engine, the defaults when generating a new Merb app.

### Validations

It may be a bit weird that the User model is almost empty, looking like this:

<pre>
class User
  include DataMapper::Resource

  property :id,     Serial
  property :login,  String
end
</pre>

You don't see it there, but MerbAuth already has your back for validating the presence of password, and making sure it's confirmed with the password_confirmation field. But you probably want to add that the login is unique, and perhaps validate the length of your login and password.

<pre>
class User
  include DataMapper::Resource

  property :id,     Serial
  property :login,  String

  validates_length      :login,    :within =&gt; 3..40
  validates_is_unique   :login
  validates_length      :password, :within =&gt; 4..40, :if =&gt; :password_required?
end
</pre>

Those are all pretty basic. But it's good to note that there's a :password_required? inside MerbAuth that you can use here, just as you were able to in the old days of merb-auth plugin. Add other fields such as created_at, updated_at or email and add more validations as you see fit.

### Signup page

Create a new.html.erb file under app/views/users:

<pre>
&lt;%= error_messages_for @user %&gt;
&lt;%= form_for @user, :action =&gt; url(:users) do %&gt;
  &lt;p&gt;
    &lt;%= text_field :login, :label =&gt; "Login" %&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;%= password_field :password, :label =&gt; "Password" %&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;%= password_field :password_confirmation, :label =&gt; "Password Confirmation" %&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;%= submit "Sign up" %&gt;
  &lt;/p&gt;
&lt;% end =%&gt;
</pre>

It's very similar to Rails but there are subtle things to note here. First, we use 'form_for' because we have a user resource for the form fields, just like in Rails. If it were a custom form that's not based off of a resource, we'd use 'form' (Merb's counterpart to Rails' 'form_tag'). For Merb, it's important that you don't miss the = in the '&lt;%= form_for ... end =%&gt;' or you would only see a blank Signup page.

Second, we have the field helpers. We had text_control, password_control and submit_button in the old Merb but now, we have text_field, password_field and submit and I think the view looks better. These same helpers are used even if you're using 'form' instead of 'form_for', unlike having to use a different set of *_tag helpers in Rails. Note also the :label option of the field helper.

### Users controller

Run 'merb-gen controller users' or create a users.rb file under app/controllers:

<pre>
class Users &lt; Application

  def index
    render
  end

  def new
    only_provides :html
    @user = User.new
    display @user
  end

  def create(user)
    session.abandon!
    @user = User.new(user)
    if @user.save
      redirect "/", :message =&gt; {:notice =&gt; "Signup complete"}
    else
      message[:error] = "Signup failed"
      render :new
    end
  end
end
</pre>

A Merb controller differs with a Rails one in a lot of ways and I'll just comment on a few. Merb has [render and display methods](http://wiki.merbivore.com/faqs/what_s_the_difference_between_the_render_and_display_methods_in_a_controller). Then, there's the convenience of specifying a parameter to be stored in a variable in your action like in the create action above. In that case, the params[:user] is automatically stored in a local variable named user.

In Merb, your controller actions need to explicitly return something. If you remove 'render' in the index action above, it'll be like returning nil as your response. Return a string, like "hello world" in the Authenticated Hello World example, and it sends that string as response to the client. If you need to return an XML representation of your object, it's as easy as having the action return @object.to_xml.

Last thing to note, we call session.abandon! in our create action to clear the session, practically logging out the user. This method is provided by MerbAuth.

### Routing

You may add the following to your router.rb file:

<pre>
  resources :users
  match('/signup').to(:controller =&gt; 'users', :action =&gt; 'new')
</pre>

### That's it!

I hope this makes a good companion to the Authenticated Hello World recipe, and does it's job of showing what you can do next with MerbAuth while introducing some of the basic concepts in Merb. There's more to MerbAuth than what we've covered here. You might want to check [its RDoc](http://merbivore.com/documentation/current/doc/rdoc/merb-auth/index.html). There's even the concept of MerbAuth Strategy (see [hassox's MerbAuth slides](http://github.com/hassox/merb-camp-talks/tree/master/Presentations/MerbAuth.pdf)) I haven't explored yet.
