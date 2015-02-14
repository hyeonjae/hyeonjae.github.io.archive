---
layout: post
title:  "Let's start to Jekyll!"
date:   2015-02-07 19:42:00
categories: jekyll update
---
Getting started with Jekyll in github.io

# Ruby
{% highlight sh %}
$ yum remove ruby ruby-devel
$ yum groupinstall "Development Tools"
$ yum install openssl-devel
$ wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz
$ tar xvfz ruby-2.1.2.tar.gz
$ cd ruby-2.1.2
$ ./configure
$ make && make install
$ yum install rubygems
$ yum install ruby-devel
{% endhighlight %}


# Jekyll
{% highlight sh %}
$ gem install jekyll
$ gem install bundler
$ gem install therubyracer

$ cd ~
$ jekyll new my-awesome-site
{% endhighlight %}

