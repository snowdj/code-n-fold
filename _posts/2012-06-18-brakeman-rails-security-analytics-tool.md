---
layout: default
title: Brakeman - Rails security analytics tool
date: 2012-06-18
categories:
- Coding
- Rails
- Tools
tags:
- analysis
- audit
- rails
- security
status: publish
type: post
published: true

---
<a href="https://codenfold.files.wordpress.com/2012/06/brakeman.png"><img class="alignright size-full wp-image-236" title="brakeman" src="assets/brakeman.png" alt="" width="225" height="307" /></a>
<a href="https://github.com/presidentbeef/brakeman">Brakeman</a> is a nice tool for static security analysis of RoR applications. It should help you discover security flaws in your applications like possible SQL injections, mass assignment problems and so on...
Simply install the gem using
````
gem install brakeman
````
and run it with a nice html output inside your rails project directory:
````
brakeman -o brakeman.html
````
The html output provides a nicely formatted frontend with summary and detail reports concerning the tests done and possible security flaws. It even displays the correspondent source code lines.
Although it doesn't give you complete safety, it's a good start for finding some potential security holes. Definitively a tool worth trying.
