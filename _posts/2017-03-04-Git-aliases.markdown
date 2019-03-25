---
title: "Git aliases"
last_modified_at: 2017-03-04T11:20:00+01:00
header: 
  overlay_image: /assets/images/post/002-git alias/bg-post.jpg
classes: wide
excerpt: "Faster dev, happier dev"
categories:
  - Android
tags:
  - Git
---

We all use git to keep track of our code base right? 
All gone are the times when CVS or SVN were used to track our projects and keep remote copies with a more or less organised history.

Since we are familiar with git we also know about its fundamental commands (like commit, checkout, etc.) and the way to use them right?

So what could we do more? Since git is a friend we work with daily, if not hourly (and if this is not the case perhaps there may be something wrong with your approach to coding, especially in teams of more than one/two members), we could benefit from small tweaks that could save us some typing and some seconds for each time that we go typing some commands.

Introducing...

# Git alias

An alias in git is, simply put, a shortcut to another command (or commands).

The way they work is simple:
First of all we need to open the file in the system root folder called **.gitconfig** in an editor (vim in the Mac bash is great).
Then we will see (and if not we can create it) a section called **[alias]** where we can input our aliases.

{% capture notice-2 %}
The way to specify an alias is:

* Write the text we want to type to call the command
* The sign '='
* Write the git command(s) we want executed
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

In case the alias is grouping more than one git command, instead of simply typing the list of commands we need to tweak slightly the structure to become:

{% highlight bash %}
! git command1 && git command2 && :
{% endhighlight %}

here are few of my aliases I constantly use

{% highlight bash %}
[alias]
    a = add
    b = branch
    c = commit
    co = checkout
    d = diff
    f = fetch -p
    g = git
    la = !git config -l | grep alias | cut -c 7-
    lg = log --graph --pretty='%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --all
    pl = pull
    ps = push
    pf = push -f
    r = reset
    rh = reset --hard
    rw = commit --amend --no-edit
    rwa = ! git add . && git commit --amend --no-edit && :
    rb = rebase
{% endhighlight %}

Then we can call them directly from the command line instead of our normal git commands

{% highlight bash %}
~ » g a . // adds all modified files to staging
~ » g c -m "First commit" // makes a commit of the files in staging and writes a message for it
~ » etc...
{% endhighlight %}

Two more complicated aliases among mine are the following

{% highlight bash %}
la = !git config -l | grep alias | cut -c 7-

lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative
{% endhighlight %}

And they respectively show the list of all the configured aliases (in case we forget what means what) and show a compact and more meaningful log of the las commits (as home exercise try it out and see if you like the new log style)

It may take a bit before getting used to them but in time the acquired speed will be definitely noticeable