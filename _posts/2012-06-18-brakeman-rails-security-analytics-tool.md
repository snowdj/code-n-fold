---
layout: default
title: Brakeman - Rails security analytics tool
date: 2012-06-18
category: Programming
tags:
- analysis
- audit
- rails
- security
type: post
published: true
excerpt: Check your application for security flaws

---

<a href="https://github.com/presidentbeef/brakeman">Brakeman</a> is a nice tool for static security analysis of RoR applications. It should help you discover security flaws in your applications like possible SQL injections, mass assignment problems and so on...
Simply install the gem using

{% highlight bash %}
gem install brakeman
{% endhighlight %}

and run it with a nice html output inside your rails project directory:

{% highlight bash %}
brakeman -o brakeman.html
{% endhighlight %}

The html output provides a nicely formatted frontend with summary and detail reports concerning the tests done and possible security flaws. It even displays the correspondent source code lines.


Although it doesn't give you complete safety, it's a good start for finding some potential security holes. Definitively a tool worth trying.
