--- 
wordpress_id: 15
layout: post
title: Grails and Rails
tags: 
- slug: ruby
  title: Ruby
- slug: rails
  title: Rails
- slug: datamapper
  title: datamapper
- slug: groovy
  title: groovy
- slug: java
  title: java
- slug: grails
  title: grails
- slug: hsqldb
  title: hsqldb
categories: 
- slug: rails
  title: Rails
wordpress_url: http://devblog.michaelgalero.com/2008/02/12/grails-and-rails/
---

I didn't like the idea of checking out [Grails](http://grails.org). I mean if I'm already a Rails developer, why bother checking a copycat. That's why I'm surprised I found things I liked about it. But will I leave Ruby on Rails for it? This article is about my first impressions of Grails after reading the book [Getting Started with Grails](http://www.infoq.com/minibooks/grails), sections of their [online documentation](http://grails.org/doc/1.0.x/) and some Grails-related articles.

### Some Good Stuff

After you run 'grails run-app' (the equivalent of 'script/server' in Rails) you can access your application with the URL http://localhost:8080/app_name and get a 'Welcome to Grails' page. It's a minor thing but I like having the application name in there. When you type your application URL by hand in the address bar and you're shown the cached URLs, it looks more organized. Multiple Grails applications can run together without having to reconfigure URLs. The Welcome page also includes links to the index page of each of your controller so that's another nice addition. (Note: The URLs to the other pages of a Grails application is accessed by appending controller/action/id to the URL, just like Rails.)

Grails ships with [HSQLDB](http://www.hsqldb.org/) which makes playing with simple applications really easy. You can create your app at the start without thinking about the database. Of course, your data won't persist this way because HSQLDB stores your data in memory, but I don't really care about that when I'm just playing around. In Rails 2.0.2, the default DB was changed to SQLite3 so you don't have to setup your database but you need SQLite3. In Grail's HSQLDB, you only need Java which you should already have if you're using Grails.

In Grails, models are 'domain classes'. Grails domain classes remind me of [DataMapper, the Ruby ORM](http://datamapper.org/). You list down the properties of the domain in the class, and the framework generates the table with these columns in the database. So instead of adding a column through a migration, just add a property in your domain class. You won't need an Annotate Models plugin here.

And there are other nice things in Grails. Because Grails is on top of Java, you have i18n support out of the box. The Grails tag library is also a clean approach to views.

### Some Bad Stuff

I have a few issues with Grails defaults. The first problem I had with Grails is that the generated DB columns are required or cannot be NULL. You need to declare in your domain class the columns that are optional. I don't really get this because in most of the applications I've worked with, there are more optional columns than required ones. Also, I'm the type of Rails developer that avoids adding constraints in the DB unless I have to. If my application is the only thing that accesses my database, then I'll put my constraints in one place, the application-side.

One of the many little things I like about Rails is if you have created_at and updated_at columns, they are automatically set when you save a record. You also have the timestamps method added in your generated model migration by default that adds these 2 columns. In Grails, you need to specify a createdAt property in your domain class, and set it manually to new Date().

In contrast to Rails, Grails controllers do not extend an application controller by default. It's a minor thing but I would probably need to extend it manually for every Grails application I write, hypothetically speaking.

In Grails, you're not supposed to need to restart your server when you make changes. But this is not entirely true. When you add static constraints to your domain classes, add new messages, or a new Java class, you still need to restart. For Java developers, it's better than having to build and deploy a WAR file every time, but it's still a bit irritating to me.

You can mix Java and Groovy in your Grails application. This can be good in a lot of ways but I think it can also be bad. Java developers working on a Grails application may be tempted to use their old, inefficient ways instead of taking the leap to Groovy. Or when Groovy doesn't support a Java construct a Java developer is used to, you end up with verbose code, like the search implementation in the Getting Started with Grails book (see Working with Java Classes starting at page 63). This was particularly disturbing and made me think: if Java always has Groovy's back, Groovy might depend on Java too much.

### It's Really the Syntax

My issues with Grails so far are minor but the thing for me is: Ruby/Rails syntax is just more beautiful than Java/Groovy/Grails syntax.

Mapping a has_many association in Rails:

<pre>
  has_many :registrations
</pre>

In Grails:

<pre>
  static hasMany = [registrations:Registration]
</pre>

Adding a before filter in your controller in Rails:

<pre>
  before_filter :auth, :except =&gt; 'search'
</pre>

In Grails:

<pre>
  def beforeInterceptor = [action:this.&amp;auth, except:['search']]
</pre>

### The end of Rails?

I won't be surprised if Grails becomes more popular than Rails but only because Java is currently the most popular language in the industry (based on [TIOBE](http://www.tiobe.com/tpci.htm)). Grails is for Java developers who don't want to leave their comfort zone. It offers something a lot better than the current Java web frameworks and that's a good thing. But I like Rails better; I might only consider Grails if I have to integrate with Java. And I prefer Ruby over Groovy. Groovy is the dynamic language that looks like Java so that Java developers have an easier transition. That has a cost though, and one may be that it will never be as beautiful as Ruby.
