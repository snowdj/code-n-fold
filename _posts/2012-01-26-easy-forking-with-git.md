---
layout: default
title: Easy forking with git
date: 2012-01-26 07:00:46.000000000 +01:00
category: Programming
tags:
- bash
- git
type: post
excerpt: Did you ever wanted to fork a project but weren't exactly sure about handling updates from the master repository?
---


With git and its aliases you can easily fetch updates from both your fork and master repository, merging them with **one git alias**:

{% highlight bash %}

git config alias.forkup '!git fetch origin -v; git fetch upstream -v; git merge upstream/master'

{% endhighlight %}

If you're a git pro and feel you're done now that's great! But if you'd like to know how the whole fork setup works read on.

Here's the detailed explanation on how to setup the fork repository and link it with its master:

Let's **initialize** a new repository for your fork:

{% highlight bash %}

mkdir myfork
cd myfork
git init

{% endhighlight %}

<strong>OR</strong> if you start with a bare <strong>remote</strong> repository, just clone it to your machine:

{% highlight bash %}

git clone git@yourreposerver.com:myfork.git myfork

{% endhighlight %}

Next add the remote <strong>master</strong> repository you want to fork from to the local git configuration:

{% highlight bash %}

git remote add upstream git@masterserver.com:masterproject.git

{% endhighlight %}

(You can, of course, change the remotes name from upstream to anything you like... The current remotes can be listed with <code> git remote -v</code>)

Now you're now ready to <strong>fetch changes</strong> from the upstream repository:

{% highlight bash %}

git fetch upstream

{% endhighlight %}

Pushing still works as ever, just push your changes to your remote with <code>git push origin master</code>.

With the little git alias at the top you're able to easily fetch and merge the master repository and thus stay always up to date.
The alias uses the verbose mode so you see what's happening....

{% highlight bash %}

git config alias.forkup '!git fetch origin -v; git fetch upstream -v; git merge upstream/master'

{% endhighlight %}

Add <code>--global</code> if the alias is suppost to be available in all your git projects.

That's all there is to it, easy and fast: the git way!

If you prefer bash aliases over the ones provided by git this should satisfy you:

{% highlight bash %}

alias gfu='git fetch origin -v; git fetch upstream -v; git merge upstream/master'

{% endhighlight %}
