--- 
wordpress_id: 23
layout: post
title: Setting up your Ubuntu Server for Merb
tags: 
- slug: gems
  title: gems
- slug: ruby
  title: Ruby
- slug: datamapper
  title: datamapper
- slug: ubuntu
  title: ubuntu
- slug: apache
  title: apache
- slug: mysql
  title: mysql
- slug: merb
  title: Merb
- slug: sqlite
  title: sqlite
- slug: passenger
  title: passenger
- slug: virtualbox
  title: virtualbox
categories: 
- slug: open-source
  title: Open Source
- slug: ruby
  title: Ruby
- slug: merb
  title: Merb
wordpress_url: http://devblog.michaelgalero.com/2008/11/27/setting-up-your-ubuntu-server-for-merb/
---
I prepared a documentation about this for a company and I thought I might as well post it here (modified a bit). I had to make sure the setup worked from scratch so I tested it using [Sun's VirtualBox](http://www.virtualbox.org/).### Table of Contents
1. Ubuntu Server
2. Package Manager: apt-get
3. Ruby
4. RubyGems
5. Apache 2
6. MySQL
7. Merb + DataMapper (+ SQLite 3?)
8. Phusion Passenger
9. Deploy the Merb App
### 1. Ubuntu Server
This setup was tested on the Ubuntu Server OS Hardy Heron, the latest version with LTS as of writing. Go to the [Ubuntu website](http://www.ubuntu.com) to download the installer of the Ubuntu version you want, preferably the latest with LTS.The following instructions may work in other Debian-based OSes because it relies mainly on the apt-get package manager.### 2. Package Manager: apt-get
_Attribution: The content of this section, Package Manager: apt-get, is copied from Configure the Package Manager section of this [Slicehost wiki page](http://wiki.slicehost.com/doku.php?id=get_started_with_your_new_ubuntu_slice)._Ubuntu's package management is done through apt-get. But it starts out handicapped. You need to edit a configuration file to add some additional sources.<pre>sudo nano /etc/apt/sources.list</pre>Uncomment these lines (remove the "# "; ignore if not commented out).<pre># deb http://archive.ubuntu.com/ubuntu/ hardy main restricted universe# deb-src http://archive.ubuntu.com/ubuntu/ hardy main restricted universe...# deb http://security.ubuntu.com/ubuntu hardy-security main restricted universe# deb-src http://security.ubuntu.com/ubuntu hardy-security main restricted universe</pre>Now update the repository index and upgrade your built-in software:<pre>sudo apt-get update &amp;&amp; sudo apt-get upgrade</pre>### 3. Ruby
To install Ruby, execute the following in the server's command line:<pre>sudo apt-get install ruby1.8-dev ruby1.8 ri1.8 rdoc1.8 irb1.8 libreadline-ruby1.8 libruby1.8 libopenssl-rubysudo ln -s /usr/bin/ruby1.8 /usr/local/bin/rubysudo ln -s /usr/bin/ri1.8 /usr/local/bin/risudo ln -s /usr/bin/rdoc1.8 /usr/local/bin/rdocsudo ln -s /usr/bin/irb1.8 /usr/local/bin/irb</pre>### 4. RubyGems
First, install the essential tools for compiling:<pre>sudo apt-get install build-essential</pre>Then, install RubyGems (latest is version 1.3.1 as of writing; update the script if necessary):<pre>mkdir sources; cd sourceswget http://rubyforge.org/frs/download.php/45905/rubygems-1.3.1.tgztar zxvf rubygems-1.3.1.tgzcd rubygems-1.3.1sudo ruby setup.rbcd ~sudo ln -s /usr/bin/gem1.8 /usr/local/bin/gemsudo gem update --system</pre>### 5. Apache 2
To install Apache, simply execute the following in the server's command line:<pre>sudo apt-get install apache2</pre>### 6. MySQL
To install MySQL:<pre>sudo apt-get install mysql-server mysql-client</pre>Note: During installation, specify the root password for the MySQL when it's asked.### 7. Merb + DataMapper (+ SQLite 3?)
Unfortunately, installing the latest version of Merb (1.0 as of writing) requires SQLite 3. So for the moment,<pre>sudo apt-get install sqlite3 libsqlite3-dev</pre>Then, to install Merb and make it work with MySQL:<pre>sudo gem install merbsudo apt-get install libmysqlclient15-devsudo gem install do_mysql</pre>### 8. Phusion Passenger
To install Passenger, do the following (from [Phusion Passenger's install page](http://www.modrails.com/install.html)):1. Open a terminal and type:<pre>sudo gem install passenger</pre>
2. Type:<pre>sudo passenger-install-apache2-module</pre>And follow the instructions.
Re-run 'passenger-install-apache2-module' if you were asked to install other dependencies. For example, if you followed the instructions in this document, you will probably be asked to install development libraries of apache2, so:<pre>sudo apt-get install apache2-prefork-devsudo passenger-install-apache2-module</pre>After that, you will probably be asked to edit your Apache configuration file (see /etc/apache2/httpd.conf) to add the following (note that version numbers may vary):<pre>LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-2.0.3/ext/apache2/mod_passenger.soPassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-2.0.3PassengerRuby /usr/bin/ruby1.8</pre>Since a Merb app is a Rack-based Ruby application, check out section 4 (or "Deploying a Rack-based Ruby Application" section) of the [Phusion Passenger User's Guide](http://www.modrails.com/documentation/Users%20guide.html). From section 4.2 (or "Deploying to a virtual host's root" section),_Add a virtual host entry to your Apache configuration file. The virtual host's document root must point to the application's public folder. For example,_<pre>  &lt;VirtualHost *:80&gt;    ServerName www.yourdomain.com    DocumentRoot /var/www/apps/my_app/public  &lt;/VirtualHost&gt;</pre>Your Merb app needs to satisfy a certain directory layout for Passenger to work. This is described in section 4 of the User's guide. In the root directory of your application, you need a public folder (which a standard Merb app should already have), a tmp folder (simply create an empty one), and a config.ru file containing the configuration detailed in section 4.5.4 of the User's guide.The Phusion Passenger User's Guide is quite a comprehensive documentation. If you encounter any problems, be sure to check its other sections like the one on Troubleshooting. For example, if you have static assets (such as stylesheets) in your application's public folder, you are likely to encounter the problem described in section 6.3.4. The solution is also there.### 9. Deploy the Merb App
Basically, to get your Merb App to running you only need to do the following:1. Make sure the gem dependencies are satisfied by either installing the gems in your server, or freezing them in your app.
2. Make sure your app satisfies the requirements of Passenger (i.e. tmp and public folders and config.ru file, see Phusion Passenger section above).
3. Place a copy of the application in an appropriate directory such as /var/www/apps/my_app. Wherever it is, make sure that it is consistent with the specified directory in the Apache configuration file (see Phusion Passenger section above).
4. Prepare your database: create it, configure your database.yml, and migrate your tables and data.
5. Start your Apache server.<pre>sudo apache2ctl start</pre>Note that with Passenger, restarting your app is done by creating a restart.txt file in the Merb app's tmp folder.
That should get you started. If you want more, there's a great talk about Deploying a Merb App by Lindsay and you can download it from the [MerbCamp videos page](http://www.merbcamp.com/video). It talks about freezing Merb and other gems, web servers, restarting your app, monitoring, configuration management, exception handling, and some other tips.
