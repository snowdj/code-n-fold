---
layout: default
title: Install Node.js on Centos 5.5
date: 2012-04-20
category: Programming
tags:
- centos
- snippet
type: post
excerpt: How to install node.js on Centos 5.5 Server?
---

Quite simple actually, login as root and do the following (you need the openssl-devel package installed in order to install node.js):

{% highlight bash %}

cd /usr/local/src
wget https://nodejs.org/dist/v0.6.19/node-v0.6.19.tar.gz
tar zxf node-v0.6.19.tar.gz
cd node-v0.6.19
./configure
make &amp;&amp; make install

{% endhighlight %}

And you're done!
Note: Newer versions are a lot more complex to install because the centos5.5 python version is - as everything else - out of date...
