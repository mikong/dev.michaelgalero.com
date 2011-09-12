--- 
wordpress_id: 20
layout: post
title: Quick notes for setting up a secure remote Git repository
tags: 
- slug: git
  title: git
- slug: setup
  title: setup
categories: 
- slug: open-source
  title: Open Source
wordpress_url: http://devblog.michaelgalero.com/2008/08/29/quick-notes-for-setting-up-a-secure-remote-git-repository/
---

Toolman Tim wrote a good article about [setting up a remote Git repository](http://toolmantim.com/article/2007/12/5/setting_up_a_new_remote_git_repository). It didn't include the part of creating a git user, so I've created my own notes below. His article does offer more explanation on the setup so be sure to check it out.

Prepare the bare Git repo:

<pre>
$ ssh myserver.com
$ mkdir /var/git
$ mkdir /var/git/myapp.git
$ cd /var/git/myapp.git
$ git --bare init
</pre>

Create your git user:

<pre>
$ addgroup git
$ adduser -g git git
$ passwd git
$ chown -R git:git /var/git/myapp.git
</pre>

Copy your local computer's public key to the git user's authorized keys:

<pre>
$ vi ~/../git/.ssh/authorized_keys
</pre>

Locate your git-shell:

<pre>
$ which git-shell
/usr/local/bin/git-shell
</pre>

And change your git user's shell from /bin/bash to the git-shell path:

<pre>
$ vi /etc/passwd
</pre>

On your local computer, go to your project directory and point it to the remote server:

<pre>
$ cd ~/dev/myapp
$ git remote add origin ssh://git@myserver.com/var/git/myapp.git
$ git push origin master
</pre>

To set the remote repository as the default branch to push and pull to (so you don't have to specify "origin master" with every push, pull, etc), open your project's Git config:

<pre>
$ vi ~/dev/myapp/.git/config
</pre>

And add the following:

<pre>
  [branch "master"]
    remote = origin
    merge = refs/heads/master
</pre>

And that's all there is to it!
