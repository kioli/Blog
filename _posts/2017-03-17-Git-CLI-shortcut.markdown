---
title: "Git CLI shortcut"
last_modified_at: 2017-03-17T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/003-git shortcut/bg-post.jpg
classes: wide
excerpt: "Saving some typing (Git aliases are so 2015...)"
categories:
  - Android
tags:
  - Bash
  - Git
---

Actually I should say they are so 2005 but... oh well

I decided to follow up on my previous post about git shortcuts for the people who want to save even more time while typing over and over commands that we developers get to use multiple times per day

In fact what we could do is not only to shorten some commands like 

{% highlight bash %}
git rb // meaning git rebase
{% endhighlight %}

but we can do so much more going to tinker about the bash config file :)

Now, in my case I use the sweet framework ZSH powered up with [Oh-My-Zsh][zsh], and its configuration can be found in your root folder in a hidden file called .zshrc
If we open it in a text editor we can paste in there the following to start saving some sweet time while typing

{% highlight bash %}
alias g='git'
alias ga='git add'
alias gaa='git add --all'
alias gb='git branch'
alias gbd='git branch -d'
alias gc='git commit -v'
alias gcm='git commit -v -m'
alias gcl='git clean -fd'
alias go='git checkout'
alias gd='git diff'
alias gf='git fetch -p'
alias gl="git log --graph --pretty='%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --all"
alias gp='git push'
alias gpf='git push -f'
alias gpl='git pull'
alias gpsup='git push --set-upstream origin $(git_current_branch)'
alias grb='git rebase'
alias grba='git rebase --abort'
alias grbc='git rebase --continue'
alias grbi='git rebase -i'
alias grh='git reset --hard'
alias gs='git status'
alias rwall='git add . && git commit -v --amend --no-edit'
{% endhighlight %}

And you can add many more to cover stashing, merging, bisecting and all other commands I just don't use often enough to remember the aliases :p

[zsh]: http://ohmyz.sh/