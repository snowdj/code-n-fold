---
layout: default
title: Fix wrong UTF-8 encodings in MySQL table
date: 2012-04-12
categories:
- SQL
tags:
- encoding
- fix
- software
- sql alter table
- utf8
type: post
excerpt: Ever had problems with wrongly encoded characters in a mysql table? You're not alone! 3 easy sql commands will help...

---
## Problem

I recently had the problem with wrongly encoded chinese (and many other) characters which looked like this:

<pre>å¤šé€‰åž‹ PersonalFit ä¸­å·ã€å¤§å·ã€è¶…å¤§å·</pre>

## Solution

I've tried several ways to fix the encodings but one easy solution solves your problem:
You have to **convert** the affected table columns three times.

1. Convert to latin1
2. Convert to blob
3. Convert back to utf8<

Let's say you have a column called <em>name</em> in your table. Here's the sql script to do the trick:

{% highlight sql %}

ALTER TABLE products CHANGE name name TEXT CHARACTER SET latin1;
ALTER TABLE products CHANGE name name BLOB;
ALTER TABLE products CHANGE name name TEXT CHARACTER SET utf8;

{% endhighlight %}

After checking the values again the encoding is now correct:
<pre>多选型 PersonalFit 中号、大号、超大号</pre>

I hope this solves your problem too - enjoy!
