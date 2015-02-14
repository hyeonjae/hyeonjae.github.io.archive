---
layout: post
title:  "Linux commands"
date:   2015-02-08 19:08:00
categories: linux
---
리눅스 기초 커맨드

리눅스는 기본적으로 여러 사용자(user)가 함께 사용하는 운영체제 입니다.

# root
파일 시스템 최상위 / 를 의미, windows의 C:\\ 에 해당
또는 관리자 계정을 의미

# 명령어 옵션
대부분의 명령어는 -- 나 -를 사용해서 옵션을 지정 할 수 있다.
gcc --version, gcc -v
보통 long option은 --를 쓰고
축약형인 short option은 -를 쓴다.
java -version		# 특이한 형태

# cd 명령어 
change directory
현재 경로를 바꾸는 명령어

{% highlight sh %}
$ cd /			# 루트로 이동
$ cd /etc		# 루트 아래 etc 디렉토리로 이동
$ cd java		# 현재 경로의 java 디렉토리로 이동
$ cd ../
$ cd ../../
$ cd ./var
{% endhighlight %}

cd java 와 cd ./java 는 같은 의미
cd 에서만 ./을 생략해도 현재 경로라는 의미가 있지만,
그외의 경우에는 ./를 반드시 써야 현재 경로로 인식함

# rm 명령어
remove 파일 삭제
{% highlight sh %}
$ rm hello.txt		# hello.txt 파일 삭제
$ rm -f hello.txt	# hello.txt 파일 삭제, [y/N] 을 물어보지 않고 삭제
$ rm -rf happy		# happy 폴더 아래 모든 파일을 삭제
{% endhighlight %}

# rmdir, mkdir 명령어
디렉토리를 삭제하거나, 생성한다.

# 기타
{% highlight sh %}
$ uname -a
{% endhighlight %}

{% highlight sh %}
$ ls -al
{% endhighlight %}
a는 숨김파일(.으로 시작하는 파일)도 보여준다.
l은 파일명만 보여주는 것이 아닌, 자세한 내용을 보여준다.

{% highlight sh %}
$ updatedb
$ location
{% endhighlight %}
파일을 빠르게 색인한다.

{% highlight sh %}
$ pwd
$ pwd -LP
{% endhighlight %}
print working directory
현재 경로를 보여준다.
pwd -LP 옵션으로 실제 경로를 보여준다.

{% highlight sh %}
ln -s <origin> <link>
{% endhighlight %}


