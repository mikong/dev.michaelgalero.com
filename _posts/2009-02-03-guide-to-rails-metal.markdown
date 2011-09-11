--- 
wordpress_id: 24
layout: post
title: Guide to Rails Metal
tags: 
- slug: ruby
  title: Ruby
- slug: rails
  title: Rails
- slug: metal
  title: metal
- slug: security
  title: security
- slug: tutorial
  title: tutorial
categories: 
- slug: rails
  title: Rails
- slug: ruby
  title: Ruby
wordpress_url: http://devblog.michaelgalero.com/2009/02/03/guide-to-rails-metal/
---

Writing a Rails Metal app can make you realize just how spoiled we've become with all the convenience that comes with Rails. Without the controller and view helpers, it can become a painful experience. Here's a guide to help make it a better experience.

For this guide, we're writing a Widget Refresher Metal app. Supposedly, the widget page of our Rails application gets called too much, and so we want to take advantage of Metal. Under our project's app/metal folder, we create refresher.rb:

<pre>
  class Refresher &lt; Rails::Rack::Metal
    def self.call(env)
      refresher = RefresherApp.new
      refresher.call(env)
    end
  end

  class RefresherApp
    def call(env)
      # refresh widget path: /widgets/:id/refresh
      if env["PATH_INFO"] =~ /^\/widgets\/(\d+)\/refresh/
        widget_id = $1
        prepare(env, widget_id)
        refresh
      else
        [404, { "Content-Type" =&gt; "text/html" }, "Not Found"]
      end
    end

    # to setup the environment
    def prepare(env, widget_id)
      ...
    end

    # the heart of our Metal app
    def refresh
      ...
    end
  end
</pre>

I like to create a separate class RefresherApp instead of just writing all of it inside the Refresher class (the one that extends from Rails::Rack::Metal). When your Metal app becomes more than just a trivial hello world app, you'll be needing a bunch of methods calling each other. Since the call method in the Metal app is a class method, putting all the code in one class will require all these methods to be class methods as well. And I think that looks ugly. Feel free to stick it all in one class if you want. If you do, you can change the context to self so you don't have to keep on defining each method as self.method:

<pre>
  class Refresher &lt; Rails::Rack::Metal

    # the methods in here are class methods
    class &lt;&lt; self
      def call(env)
        ...
      end

      def method
        ...
      end
    end

  end
</pre>

For the rest of the guide, we're using my approach. Also, note that when developing a Metal app, you need to keep on restarting your server for your code changes to take effect.

### Request and Session

To access the request and the parameters in it, you can use this code:

<pre>
  request = Rack::Request.new(env)
  params = request.params
  params['mykey'] # String keys, so not params[:mykey]
</pre>

As you can see, the keys will be of class String, not Symbol. Now for the session, you can get it from the environment:

<pre>
  session = env['rack.session']
</pre>

We can move all these code into our prepare method. In addition, we can set the params[:id] (using a Symbol if you want), so that in our main refresh method, it would be like in a Rails controller. With the session, we can get the current user. We can also define other methods to make things more like writing code for a Rails controller. This is how it looks like:

<pre>
  attr_reader :request, :session, :current_user

  def params
    @request.params
  end

  def logged_in?
    !!current_user
  end

  def prepare(env, widget_id)
    @request = Rack::Request.new(env)
    params[:id] = widget_id
    @session = env["rack.session"]
    @current_user = session[:user_id] ? User.find(session[:user_id]) : false
  end
</pre>

With these out of the way, we go into writing the code for the main method called refresh.

### refresh and ActiveRecord

ActiveRecord works out of the box, no setup needed. Cool! Let's say we just need to return the status of widget to the client side:

<pre>
  def refresh
    @widget = Widget.find(params[:id])

    return [200, { "Content-Type" =&gt; "text/html" }, @widget.status]
  end
</pre>

We can also send javascript code, or other content types back to our client. Just make sure to set your content type properly. Let's also add some simple checking if the user is logged in:

<pre>
  def refresh
    @widget = Widget.find(params[:id])

    if logged_in?
      return [200, { "Content-Type" =&gt; "text/javascript" }, "Element.update('status', '#{@widget.status}');"]
    else
      return [200, { "Content-Type" =&gt; "text/javascript" }, "Element.update('message', 'Must be logged in for widget status to refresh');"]
    end
  end
</pre>

When returning more complex javascript however, it's probably better to escape the newlines and the quotes or we'll get parsing errors on the browser side. Rails provides a helper method called escape_javascript, but a Metal app doesn't have access to helpers by default. So...

### View Helpers

To use helpers in your Metal app, just include the modules you need:

<pre>
  include ActionView::Helpers::JavascriptHelper # so escape_javascript works
  include WidgetsHelper # for example
</pre>

I prefer to avoid including too much of these helpers though.

### Request Forgery Protection

If the request is not a GET request, we may need to verify the authenticity token. Here's one way to do it:

<pre>
  def refresh
    # before everything else
    return redirect_to_widgets_response unless verified_request?

    # everything else
    ...
  end

  def redirect_to_widgets_response
    return [302, { "Content-Type" =&gt; "text/html", "Location" =&gt; "/widgets" },
    "&lt;html&gt;&lt;body&gt;&lt;a href=\"/widgets\"&gt;Redirecting...&lt;/a&gt;&lt;/body&gt;&lt;/html&gt;"]
  end

  # Based on Rails method of the same name but simplified, i.e. no need to check if:
  #   - protection is disabled
  #   - request method is :post
  #   - format is verifiable
  def verified_request?
    form_authenticity_token == params['authenticity_token']
  end

  def form_authenticity_token
    session[:_csrf_token] ||= ActiveSupport::SecureRandom.base64(32)
  end
</pre>

### More Challenges

There are other challenges you may encounter in writing your Rails Metal app. I have tried rendering a partial by directly using ERB but it's too ugly to show here. And I've also struggled with performance. Not all business logic can simply be translated to a Metal app to be fast. It is recommended for very simple things only, or it may not be worth it. Anyway, I hope this guide clears up a few things. Also, if you have better ways of doing any of the above, feel free to post in the comments. Thanks!
