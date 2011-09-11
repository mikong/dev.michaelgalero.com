--- 
wordpress_id: 12
layout: post
title: My Git notes for Rails
tags: 
- slug: rails
  title: Rails
- slug: textmate
  title: textmate
- slug: git
  title: git
- slug: giston
  title: giston
categories: 
- slug: rails
  title: Rails
wordpress_url: http://devblog.michaelgalero.com/2007/12/17/my-git-notes-for-rails/
---
From Toolman Tim's blog entry [Setting up a new Rails app with Git](http://toolmantim.com/article/2007/12/5/setting_up_a_new_rails_app_with_git) (with a few changes, and summarized for personal reference).<pre>$ git init$ mate .gitignore</pre>Add in .gitignore:<pre>log/*.logtmp/**/*.DS_Storedoc/apidoc/appconfig/database.ymldb/schema.rb</pre>Back to the command line:<pre>$ cp config/database.yml config/database.yml.example$ touch log/.gitignore$ touch tmp/.gitignore$ git add .$ git commit -m "Initial commit"</pre>### Additional Notes:
- [Git Tutorial Intro](http://www.kernel.org/pub/software/scm/git/docs/gittutorial.html)
- [Git User's Manual](http://www.kernel.org/pub/software/scm/git/docs/user-manual.html)
- [Everyday GIT with 20 Commands or so](http://www.kernel.org/pub/software/scm/git/docs/everyday.html)
- [SourceMage's Git Guide](http://wiki.sourcemage.org/Git_Guide)
- To manage edge rails, use [Giston](http://evil.che.lu/2007/11/27/ann-giston-piston-lookalike-for-git) or just delete and re-export (taken from the comments of the blog entry).
- Touch empty dirs (thanks to [Nick Poulden](http://www.midlandswebdesign.com/)'s comment):
<pre>  find . \( -type d -empty \) -and \( -not -regex ./\.git.* \) -exec touch {}/.gitignore \;</pre>
