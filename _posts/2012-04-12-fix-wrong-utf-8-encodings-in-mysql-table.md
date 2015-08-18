---
layout: default
title: Fix MySQL UTF-8 encoding
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
excerpt: Ever had problems with utf-8 encoded characters in a mysql table? You're not alone! 3 easy sql commands might help...
navigation: true

---
## Problem

I recently had the problem with wrong encoded chinese characters which looked like this:

<pre>å¤šé€‰åž‹ PersonalFit ä¸­å·ã€å¤§å·ã€è¶…å¤§å·</pre>

## Solution

I've tried several ways to fix the encodings but one easy solution solved my problem:
I had to **convert** the affected table columns three times.

1. Convert to latin1
2. Convert to blob
3. Convert back to utf8<

So, if you have a column called <em>name</em> in your table, the following sql script to do the trick:

{% highlight sql %}

ALTER TABLE products CHANGE name name TEXT CHARACTER SET latin1;
ALTER TABLE products CHANGE name name BLOB;
ALTER TABLE products CHANGE name name TEXT CHARACTER SET utf8;

{% endhighlight %}

After checking the values again the encoding should now be correct like mine:
<pre>多选型 PersonalFit 中号、大号、超大号</pre>

Hopefully now your problem's solved :)
