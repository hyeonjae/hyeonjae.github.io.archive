---
title:  "git 설치"
date:   2015-02-17 11:19:00
categories: git
---

### git 설치

git 다운로드
{% highlight sh %}
wget https://github.com/git/git/archive/v2.2.2.tar.gz
{% endhighlight %}

빌드
{% highlight sh %}
tar xvfz v2.2.2.tar.gz
cd git-2.2.2
make configure && whereis git
./configure --prefix=/usr
make all && make install
{% endhighlight %}

ssl 설정
{% highlight sh %}
git config --global http.sslVerify false && cat ~/.gitconfig
{% endhighlight %}
