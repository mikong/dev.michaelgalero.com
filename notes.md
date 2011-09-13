## Server Info
 * IP: 67.207.128.24
 * Slicename: hikaru
 * port: 2048
 * Another IP? 67.207.132.156
 * mysql -u wpadmin -p '3%,%?8/7'
 * create database mikongblog\_wpmu;
 * grant ALL on mikongblog\_wpmu.* to wpadmin;
 * Wordpress dir: /usr/local/wordpress-mu-1.3/

## Disqus
 * Signup - ok
 * register Akismet - ok
 * Install Disqus WP plugin - failed
   * `wget http://downloads.wordpress.org/plugin/disqus-comment-system.2.66.zip`
   * `cd /usr/local/wordpress-mu-1.3/wp-content/plugins && unzip ~/sources/disqus-comment-system.2.66.zip`
   * Activate plugin under Plugins tab - FATAL ERROR
 * Manual Export of comments to Disqus - ok
   * `delete from wp_2_comments where comment_approved = 'spam';`
   * Go to Manage > Export and click Download Export File
   * Go to import.disqus.com and upload WXR

dump:

    mysqldump -u wpadmin -p mikongblog_wpmu > blog_backup.sql

## Content
 * Delete drafts - ok
 * Delete ugly articles - ok
 * Backup database before importing anything (comment or blog posts) - ok
 * Delete spam comments - ok

## Import Code - OK
 * Remove drafts in import code
 * Fix table_prefix
 * verify that tables exist
 * verify that columns match
 * Remove featured images
 * Remove redundant autoslug
 * Exclude link_category
 * Fix order by error

## Import
  * Install gems
    * sequel
    * active_support
    * hpricot - for downmark_it.rb
    * mysql2 - depends on libmysqlclient package
  * Update apt-get sources.list to point to old-releases.ubuntu.com
  * Install packages
    * `apt-get install libmysqlclient15-dev`
    * `apt-get install git-core`
  * `wget https://github.com/cousine/downmark_it/raw/master/downmark_it.rb --no-check-certificate`
    * edit line for require 'hpricot'
  * `wget https://gist.github.com/gists/1194838/download --no-check-certificate`
  * `mv download import.tar.gz`
  * `tar xzvf import.tar.gz`
  * `ruby -r './import.rb' -e 'WordPress::import("mikongblog_wpmu", "wpadmin", "3%,%?8/7", "wp_2")'`
  * `scp -r hikaru:import/_posts .`

# TODO

## Jekyll Config

## Jekyll
 * Determine category\_dir and category\_title\_prefix

## Manual Fixing
 * Posts - ok
 * About - ok
 * Archives - ok
 * Files - ok
   * confreaks-rubyconf-2007.jpg
   * `scp hikaru:/usr/local/wordpress-mu-1.3/wp-content/blogs.dir/2/files/2007/12/*.jpg .`
   * upload to skitch
 * Stylesheets

## Deploy
