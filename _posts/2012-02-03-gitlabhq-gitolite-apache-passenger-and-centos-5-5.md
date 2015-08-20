---
layout: default
title: Gitlabhq, Gitolite, Apache Passenger and Centos 5.5
date: 2012-02-03
category: Programming
tags:
- git
- gitlabhq
- gitolite
type: post
excerpt: Getting the great project <a href="https://gitlabhq.com/">gitlabhq</a>, a web frontend for git based on gitolite and rails to run under Centos 5.5 can be quite frustrating but I managed to get the whole system up and running.
---

To get started I’ve read the installation manual on the gitlabhq wiki page as well as serveral other blogs and resources in order to cope with the bugging Centos 5.5 problems and outdated packages. (I’ll admit I don’t like this OS very much…)

There are quite a lot of tutorials and manuals got get gitlabhq working but most of them are either not intended for CentOS or don’t provide help for certain pitfalls…Here I’ll provide a step by step explanation on how I did it.

## Target Configuration

My intended target configuration for the gitlabhq server is as follows:

* git 1.7.4.1
* ruby 1.9.2
* mysql 5.5.20
* redis 2.4.6
* apache 2
* mod_passenger 3.0.11
* gitlabhq 2.1 stable

I use mysql as database backend instead of the pre-configured sqlite3 setup gitlabhq uses by default. It’s just the better choice for a production environment.
Preinstalled software

I expect you’ve got a Centos 5.5 System up and running with the following software preinstalled:

* apache2
* git 1.7.x
* python 2.4
* mysql 5.5

## Installation and Configuration

So let’s start with the installation and configuration process. If you follow the guide top-down you’ll have a running gitlabhq setup in notime!

We start by installing the overall prerequired CentOS and source packages.

### CentOS Packages

Firstly install required CentOS packages like the openssh-server and other packages required for the building or installation processes.

{% highlight bash %}

sudo yum install -y git openssh-server curl-devel openssl-devel httpd-devel apr-devel apr-util-devel libzlib-ruby zlib-devel python-devel python-setuptools libicu-devel gcc-c++ gcc ruby-devel libxml2 libxml2-devel libxslt libxslt-devel

{% endhighlight %}

### Source Packages

#### Libyaml

You have to compile the yaml library directly from source, there are no current packages available for CentOS.

{% highlight bash %}

wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz
tar xzvf yaml-0.1.4.tar.gz
cd yaml-0.1.4
./configure --prefix=/usr/local
make && make install

{% endhighlight %}

#### Redis

It’s the same with the redis key-value store, compile it from source to get a current version, redis is used by gitlabhq.

{% highlight bash %}

wget http://redis.googlecode.com/files/redis-2.4.6.tar.gz
tar xvfz redis-2.4.6.tar.gz
cd redis-2.4.6
make && sudo make install

{% endhighlight %}

There are sed scripts available to customize the redis standard configuration, just execute the command line statements below. It enables the redis daemon process and outputs logs to /var/log/redis.log:

{% highlight bash %}

mkdir /etc/redis /var/lib/redis
sed -e "s/^daemonize no$/daemonize yes/" -e "s/^dir \.\//dir \/var\/lib\/redis\//" -e "s/^loglevel debug$/loglevel notice/" -e "s/^logfile stdout$/logfile \/var\/log\/redis.log/" redis.conf > /etc/redis/redis.conf

{% endhighlight %}

Fortunately there is an init script for redis and Centos available, you can get it from github.com:

{% highlight bash %}

wget https://raw.github.com/gist/257849/9f1e627e0b7dbe68882fa2b7bdb1b2b263522004/redis-server
sed -i "s/usr\/local\/sbin\/redis/usr\/local\/bin\/redis/" redis-server
chmod u+x redis-server
mv redis-server /etc/init.d
/sbin/chkconfig --add redis-server
/sbin/chkconfig --level 345 redis-server on
/sbin/service redis-server start

{% endhighlight %}

** Testing Redis Installation**

Test the redis installation by opening a telnet connection and setting/reading a key value pair:

{% highlight bash %}

telnet 127.0.0.1 6379
set attitude:today "happy"
get attitude:today

{% endhighlight %}

#### Installation Resque Post-Receive Git Hooks

If you’d like to use the Redis/Reque post-receive hooks with gitlab, the following command starts resque detached from the current terminal session:

{% highlight bash %}

nohup bundle exec rake environment resque:work QUEUE=* VVERBOSE=1 RAILS_ENV=production PIDFILE=tmp/pids/resque_worker_QUEUE.pid

{% endhighlight %}

#### Sqlite3

Although I’m not using Sqlite3 in my configuration I’ll explain the installation process anyway in case you stick with Sqlite3 instead of MySQL for the database backend. As expected, the Centos 5.5 Sqlite package is outdated and has to be compiled from source:

{% highlight bash %}

wget http://www.sqlite.org/sqlite-autoconf-3070800.tar.gz
tar -zxvf sqlite-autoconf-3070800.tar.gz
cd sqlite-autoconf-3070800
./configure --prefix=/opt/sqlite3 && make && make install

{% endhighlight %}

To finish the setup process the new shared libraries have to be copied to the libraries search path:

{% highlight bash %}

cp -p /opt/sqlite3/lib/libsqlite3.so.0 /usr/lib64
cp -p /opt/sqlite3/lib/libsqlite3.so.0 /usr/lib

{% endhighlight %}

#### Ruby

As we know by now the way to go is to compile the software from source to get a current version, it’s no different for our beloved Ruby...

{% highlight bash %}

wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p290.tar.gz
tar xzvf ruby-1.9.2-p290.tar.gz
cd ruby-1.9.2-p290
./configure --prefix=/usr/local --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
make && make install

{% endhighlight %}

Add the ruby binary path (/usr/local/bin) to the gobal paths:

{% highlight bash %}

echo 'pathmunge /usr/local/bin' > /etc/profile.d/ruby.sh
chmod +x /etc/profile.d/ruby.sh

{% endhighlight %}

And update the gem system, just to be sure…

{% highlight bash %}

gem update --system

{% endhighlight %}

#### Gitolite

The gitolite installation manuals can be quite perplexing and complicated. But if you get down to it the installation process is quite straight forward:

Create a new user to be used by gitolite

{% highlight bash %}

useradd --shell /bin/bash --comment 'git version control' -m git

{% endhighlight %}

Create a corresponding group and add the newly created user to it

{% highlight bash %}

usermod -a -G git git

{% endhighlight %}

Generate an ssh-key for the newly created git user without a password!

{% highlight bash %}

su git
ssh-keygen -t rsa

{% endhighlight %}

Copy the created key to the git user home directory replacing the default name with the username

{% highlight bash %}

cp .ssh/id_rsa.pub git.pub

{% endhighlight %}

Clone the gitolite repository

{% highlight bash %}

sudo -u git -H git clone git://github.com/gitlabhq/gitolite /home/git/gitolite

{% endhighlight %}

Install gitolite by using the earlier copied git public key for the initial setup


{% highlight bash %}
sudo -u git -H /home/git/gitolite/src/gl-system-install
sudo -u git -H sh -c "PATH=/home/git/bin:$PATH; gl-setup ~/git.pub"

{% endhighlight %}

During the installation you’re prompted to edit the gitolite config file. Replace the repository umask value as described below

{% highlight bash %}

$REPO_UMASK = 0007;

{% endhighlight %}


As the last step fix the repository directory permissions and owner

{% highlight bash %}

sudo chmod -R g+rwX /home/git/repositories/
sudo chown -R git:git /home/git/repositories/

{% endhighlight %}

### Testing Gitolite

This step is mandatory in order to get the localhost entry in the ssh known_keys file made

To test the gitolite installation clone the gitolite admin repository on the server into the tmp folder.

You have to do this using the git user, only this user has its public key in gitolite installed at the moment.

{% highlight bash %}

git clone git@localhost:gitolite-admin.git /tmp/gitolite-admin

{% endhighlight %}

If everything is in order you can delete the cloned repository afterwards…

{% highlight bash %}

rm -rf /tmp/gitolite-admin

{% endhighlight %}


#### Gitlabhq

Wer’re finally getting to the gitlabhq installation. Let’s start with the prerequired python and ruby libraries:

{% highlight bash %}

easy_install pip
sudo pip install pygments
sudo gem install bundler

{% endhighlight %}

If you’re using Sqlite3 as db backend, install the sqlite3 gem with the following command

{% highlight bash %}

gem install sqlite3 -v '1.3.4' -- --with-sqlite3-dir=/opt/sqlite3

{% endhighlight %}

This is also a good chance to install the by gitlabhq required charlock holmes gem

{% highlight bash %}

sudo gem install charlock_holmes -v '0.6.8'

{% endhighlight %}

If you’re getting SSL certificate verification errors use the no verify global variable and try the gem install command again:

{% highlight bash %}

export GIT_SSL_NO_VERIFY=true

{% endhighlight %}

Now clone the gitlab repository and install the gems (as user git)

{% highlight bash %}
c
d
git clone -b stable git://github.com/gitlabhq/gitlabhq.git
cd gitlabhq

{% endhighlight %}

#### MySQL Backend

If you’re using the preconfigured Sqlite3 backend you can skip this step and proceed directly with the bundle install command further below.
But if you decide to use MySQL as database backend you’ll have to make some small changes to the gitlabhq configuration as well as initialize the mysql database and add a query user for gitlabhq.
First, edit the config/database.yml file and configure a production environment similar to this:

{% highlight bash %}

production:
  adapter: mysql2
  username: gitlab
  password: "your_db_password"
  host: localhost
  database: gitlab

{% endhighlight %}  

Second, you have to add the mysql2 gem to the Gemfile in the gitlabhq folder (you can remove the sqlite3 gem dependency)

{% highlight bash %}

gem "mysql2"

{% endhighlight %}

To be sure it works, install the mysql2 gem separately:

{% highlight bash %}

gem install mysql2

{% endhighlight %}

I expect you have a MySQL server up and running that's why I'm only describing the initial database setup.
Login into the mysql client using the root user

{% highlight bash %}

mysql -u root -p

{% endhighlight %}

Create a user and a database for gitlabhq

{% highlight sql %}

CREATE USER gitlab@localhost IDENTIFIED BY 'here_is_the_db_password';
CREATE DATABASE IF NOT EXISTS gitlab;
GRANT ALL PRIVILEGES ON gitlab . * TO gitlab@localhost;

{% endhighlight %}

Install bundle, load tables and initial data

Now proceed with the finishing touch; install the bundles and execute the initial db setup

{% highlight bash %}

bundle install --without development test
bundle exec rake db:setup RAILS_ENV=production
bundle exec rake db:seed_fu RAILS_ENV=production

{% endhighlight %}

#### Apache2 mod_passenger

I’m against running gitlabhq using the rails provided server so I’ll be using passenger instead…
Let’s assume you’re using a working apache2 installation with the module configurations stored under /etc/httpd/conf.d and a vhost setup with the vhost configurations stored under /etc/httpd/hosts.

Start by installing the passenger gem and passenger itself

{% highlight bash %}

gem install passenger
passenger-install-apache2-module

{% endhighlight %}

Copy the generated loadmodule statements to a new module file and store it under /etc/httpd/conf.d/passenger.conf

{% highlight bash %}

LoadModule passenger_module /usr/local/lib/ruby/gems/1.9.1/gems/passenger-3.0.11/ext/apache2/mod_passenger.so
PassengerRoot /usr/local/lib/ruby/gems/1.9.1/gems/passenger-3.0.11
PassengerRuby /usr/local/bin/ruby

{% endhighlight %}

Here’s an example of a working vhost configuration, you can copy paste and adjust it to your needs:

{% highlight bash %}

ServerName gitlabhq.yourhost.com
DocumentRoot /home/git/gitlabhq/public

    AllowOverride All
    Options -MultiViews

CustomLog logs/gitlab/access combined
ErrorLog  logs/gitlab/error
ServerAdmin info@yourhost.com
RackBaseURI /
RackEnv production

{% endhighlight %}

It’s extremely important to add the apache user to the git user group to prevent access problems:

{% highlight bash %}

usermod -a -G git apache

{% endhighlight %}

## Resources

I’ve used the following web resources to help me with my installation:

I've used the following web resources to help me with my installation:
<table>
<tbody>
<tr>
<th>URL</th>
<th>Description</th>
</tr>
<tr>
<td><a href="https://github.com/gitlabhq/gitlabhq/wiki/Ubuntu.stable" rel="nofollow">Gitlabhq</a></td>
<td>Ubuntu linux installation tutorial</td>
</tr>
<tr>
<td><a href="https://github.com/gitlabhq/gitlabhq/wiki/Gitolite" rel="nofollow">Gitolite (Gitlabhq)</a></td>
<td>Tutorial for gitolite installation on ubuntu linux</td>
</tr>
<tr>
<td><a href="https://sites.google.com/site/senawario/home/gitolite-tutorial" rel="nofollow">Gitolite Installation Tutorial</a></td>
<td>In depth installation tutorial for gitolite</td>
</tr>
<tr>
<td><a href="http://www.ebrueggeman.com/blog/install-redis-centos-56" rel="nofollow">Redis</a></td>
<td>Installation tutorial for redis on CentOS 5.6</td>
</tr>
<tr>
<td><a href="http://nokogiri.org/tutorials/installing_nokogiri.html" rel="nofollow">Nokogiri</a></td>
<td>Installation tutorial for Nokogiri</td>
</tr>
<tr>
<td><a href="http://www.shiftedbytes.com/2011/04/install-ruby-192-passenger-on-centos-55.html" rel="nofollow">Ruby 1.9.2 on CentOS 5.5</a></td>
<td>Installation tutorial for Ruby and Passenger on CentOS 5.5</td>
</tr>
</tbody>
</table>

## Pitfalls

One sad thing about gitlabhq is the overall 500 error page. You won’t get detailed  information on what went wrong it just says “there was a problem, check your config”.

The following thing is essential if you’re having problem like for example creating new repositories and using mod_passenger:

The user running passenger must have write access to the gitolite repository folder! If he doesn’t have the permissions – fail!

For general problems try deleting the lockfiles generated by gitlabhq in the system temp directory:

{% highlight bash %}

sudo rm -rf /tmp/gitlabhq-gitolite*

{% endhighlight %}

## Final Notes

I’m using a slightly different setup in our company mainly because I’ve forked the gitlabhq project to make some company-related changes. In order to speed up development and deployment I’m using capistrano to deploy my gitlabhq fork to the server. I’m not posting information about it here because it’s a special setup and won’t be useful to you if you’re not actively making changes to gitlabhq.

So that’s it with this you should get gitlabhq working on your CentOS 5.5 system. If you’ve got problem or comments feel free to leave them here…
