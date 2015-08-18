---
layout: default
title: Capistrano and .htaccess
date: 2012-06-25
categories:
- Coding
- Server
- Tools
tags:
- ".htaccess"
- capistrano
- enterprise-it
- production environment
- production environments
- protect
- ruby
- staging system
type: post
excerpt: Easily enable and disable htaccess basic protection via command line
navigation: true

---
Are you using capistrano to deploy your application to multiple environments?
Are you using a public staging system but like to easily control access via .htacces file?
Then this might interest you.

A little bit more context information.
I have a project with two deploy environments configured using capistrano, staging and production.
The staging environment is used for client testing before the product gets released onto the production environment.
We're hosting our staging and production environments using a 3rd party hosting provider.
Both environments are publicly accessible but - of course - the client demands restricted access on the staging environment.

Theres quite an easy way to automate .htaccess protection using capistrano.
First, add the following two tasks to your deploy.rb script:
[gist]https://gist.github.com/2988156[/gist]
<a href="https://www.htaccesstools.com/htaccess-authentication/">This website</a> provides an easy and convenient tool to generate the .htpasswd file. Use it to generate your password and replace the [USER]:[ENC_PASSWD] string above with the correct output.
Done! You're now able to protect and unprotect a deploy environment with the following two capistrano tasks:

````
cap deploy:protect
cap deploy:unprotect
````
