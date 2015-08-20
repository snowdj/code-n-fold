---
layout: default
title: GIT + Rails + Rcov + Jenkins = Continuous Integration
date: 2012-04-24 11:00:33.000000000 +02:00
category: Programming
tags:
- ci
- git hooks
- hudson
- rails
- rcov
type: post
published: true
excerpt: Simple and easy to configure continuous integration environment

---

Let's set up a **simple and easy** to configure continuous integration environment to **run automated tests and generate coverage reports** for our code.

The setup consists of three steps. In the end we'll have an automated setup which requires no further steps to use it!

## Initial Situation

We have a <a href="https://rubyonrails.org/">Ruby on Rails</a> project under <a href="https://git-scm.com/">GIT</a> source control on which we want to run tests and generate coverage reports using <a href="https://jenkins-ci.org/">Jenkins</a>.

The repository consists of several branches, but the main (and interesing) branches are **development**, **staging** and **master**.

Tests and coverage reports should be generated for the **staging** and **master** branches every time something is commited into them.

So far so good, let's get started.

## Prerequirements

I'm assuming that you already have the following environment up and running, If not - well...

* A RoR project hosted on a central GIT repository
* A GIT repository server (it also works without one, but this is the preferred setup and we're using hooks so you must be able to edit them)
* A Jenkins build server (with git and ruby running) and the following plugins installed
  <a href="https://wiki.hudson-ci.org/display/HUDSON/Ruby+Plugin">Hudson ruby plugin</a>,
  <a href="https://wiki.jenkins-ci.org/display/JENKINS/Rake+Plugin">Jenkins Rake plugin</a>,
  <a href="https://wiki.jenkins-ci.org/display/JENKINS/Ruby+Metrics+Plugin">Jenkins ruby metrics plugin</a>,
  <a href="https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin">Jenkins GIT plugin</a>

## 1st - Configure the build job

I suggest you include the rcov gem in your Gemfile under the test group to assure it's getting installed on the build server. If you don't want to, install the gem on your build server.

{% highlight ruby %}

group :test do
  ...
  gem 'rcov'
  ...
end
{% endhighlight %}

Create a new jenkins **build job** for your project and configure it.

* Select **Git** under Source-Code-Management and add your build branches. In your case they would be **staging** and **master**
* Add **execute shell** as build step and paste the following script, adjust the steps as you see fit for your case

{% highlight bash %}

# setup env
export RAILS_ENV=test

# Prepare for rcov
[ -d &quot;coverage&quot; ] &amp;&amp; rm -rf coverage
mkdir coverage

# install bundle
bundle install --deployment --without development

# prepare database
mv config/database.example.yml config/database.yml
bundle exec rake db:drop
bundle exec rake db:create
bundle exec rake db:schema:load
bundle exec rake db:seed

# run tests and coverage
bundle exec rake test
bundle exec rake rcov
{% endhighlight %}


* Check the **Publish Rcov report option under **post-build-actions** and set the **Rcov report directory** to **coverage**. Adjust the Coverage metric targets as desired but the default values are a good start.


That's it for the basic jenkins job configuration, you can of course define other options as well, I for example use e-mail notifications as well.

## 2nd - Configure git hook for to trigger build process

Now, I want my build job to be triggered every time I merge into the staging or master branch. To achieve this I'm using the git **post-update** hook. You can, of course use the hook on your local machine without using a central GIT server. It suffices if you're working alone or simply can't edit GIT hooks on your repository server (which is a sad thing).

But let's assume we have a central GIT server, so add the following script as **post-update** hook on your git server. The hooks are located in the git repository directory under **your-repository/hooks/post-update.** You can simply replace the file located there, important is the filename **post-update**.


{% highlight bash %}
#!/bin/sh
# trigger jenkins build after post-update into specific branch
#stages='staging|master'
#if [[ `git rev-parse --abbrev-ref HEAD` =~ $stages ]]; then
	curl https://hudson.yourdomain.com/job/Your-Job/build?delay=0sec
#fi
{% endhighlight %}

Finally, the hook must be marked as executable, else it won't work:

{% highlight bash %}
chmod +x post-update
{% endhighlight %}

And this part's done too!

## 3rd - Develop, merge, push

The setup is complete, every time something ist commited into the staging or master branch and pushed to the GIT server your jenkins job gets triggered which in turn runs your tests and generates rcov coverage reports.
