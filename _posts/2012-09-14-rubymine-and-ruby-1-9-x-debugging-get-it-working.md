---
layout: default
title: Rubymine and Ruby 1.9.x debugging - get it working
date: 2012-09-14 18:00:22.000000000 +02:00
category: Programming
tags:
- rubymine
- debugger
status: publish
type: post
published: true
excerpt: Get it working
---

Sadly Rubymine still hasn't resolved the debugger issue when trying to debug a Ruby 1.9.x application inside Rubymine.
It's not hard to get the debugger up and running when you know how, there's also a lot of chatter among the topic.

It boils down to the following steps:


1) Download<a href="https://rubyforge.org/frs/download.php/75414/linecache19-0.5.13.gem"> linecache19 0.5.13</a> and install it with<br />
{% highlight bash %}
gem install linecache19-0.5.13.gem
{% endhighlight %}

2) Install the 1.9.x Ruby debug base (pre-release)<br />
{% highlight bash %}
gem install ruby-debug-base19x --pre
{% endhighlight %}

3) Install the 1.9.x Ruby debug ide (also pre-release)<br />
{% highlight bash %}
gem install ruby-debug-ide19  --pre
{% endhighlight %}

4) Start Rubymine and you're set!
