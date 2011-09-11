--- 
wordpress_id: 13
layout: post
title: Learning Ruby 1.9
tags: 
- slug: ruby
  title: Ruby
- slug: rails
  title: Rails
categories: 
- slug: rails
  title: Rails
- slug: ruby
  title: Ruby
wordpress_url: http://devblog.michaelgalero.com/2007/12/25/learning-ruby-19/
---
 I'm not going to list down "improve Ruby skills" on my New Year's resolution. With the [newly released Ruby 1.9.0](http://www.ruby-lang.org/en/news/2007/12/25/ruby-1-9-0-released/) (a development release) and [beta book Programming Ruby 3 by Dave Thomas](http://pragprog.com/titles/ruby3) (or simply Pickaxe 3), what better time to dig deeper into Ruby than now?When I setup Rails on my mac a few months back (before Leopard and Rails 2.0), I followed [Building Ruby, Rails, Subversion, Mongrel, and MySQL on Mac OS X](http://hivelogic.com/articles/ruby-rails-mongrel-mysql-osx/) by [Hivelogic](hivelogic.com). Following the Ruby part of that but updated for Ruby 1.9:<pre>  cd /usr/local  sudo curl -O  ftp://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.0-0.tar.gz  sudo tar xzvf ruby-1.9.0-0.tar.gz  cd ruby-1.9.0-0  sudo ./configure --prefix=/usr/local/ruby-1.9.0 --enable-pthread --with-readline-dir=/usr/local  sudo make  sudo make install</pre>I didn't have to run 'make install-doc' because 'make install' already installed the documentation. Since I haven't upgraded to Leopard yet, this has only been verified on Tiger. Running '/usr/local/ruby-1.9.0/bin/ruby -v', I got<pre>  ruby 1.9.0 (2007-12-25 revision 14709) [i686-darwin8.11.1]</pre>Cool! And I just bought Pickaxe 3 (right after installing). Looking for the first 1.9 feature I can find in the book... a new hash syntax:<pre>  $ /usr/local/ruby-1.9.0/bin/irb  irb(main):001:0&gt; inst_section = { cello: 'string', clarinet: 'woodwind' }  =&gt; {:cello=&gt;"string", :clarinet=&gt;"woodwind"}  irb(main):002:0&gt; inst_section[:clarinet]  =&gt; "woodwind"</pre>It works! And let me just add that new hash syntax looks gorgeous. Now I have to end this article and try a 1.9 feature beyond mere syntax updates. **Chapter 11: Fibers, Threads, and Processes** looks like a good place to start... :)**Update 12/26/2007:** I initially installed Ruby 1.9 on /usr/local. It installed rubygems 1.0.1 with it and messed up my gem installation for 1.8.6. I had to reinstall 1.8.6, and then reinstall ruby 1.9 in an isolated directory, /usr/local/ruby-1.9.0. I updated the article to use this directory. For now, I only use 1.9 to try the new features. If you need to work with multiple versions of Ruby, you might want to check out [Dan Manges' article](http://www.dcmanges.com/blog/install-multiple-versions-of-ruby-on-osx-leopard).
