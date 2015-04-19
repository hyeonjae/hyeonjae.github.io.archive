---
title:  "자주 쓰는 정규식 모음"
date:   2015-03-25 13:53:42
categories: regex
---


유용한 정규식 모음

### 문자열

{% highlight javascript %}
var pattern = /"(?:[^"\\]|\\.)*"/g;
{% endhighlight %}
{% highlight javascript %}
var pattern = /"(\\.|[^\"])*"/g;
{% endhighlight %}

[http://stackoverflow.com/questions/249791/regex-for-quoted-string-with-escaping-quotes](http://stackoverflow.com/questions/249791/regex-for-quoted-string-with-escaping-quotes)
  

### C스타일 주석
{% highlight javascript %}
/((?:\/\*(?:[^*]|(?:\*+[^*\/]))*\*+\/)|(?:\/\/.*))/g;
{% endhighlight %}

[http://blog.ostermiller.org/find-comment](http://blog.ostermiller.org/find-comment)


### 안전한 비밀번호
{% highlight javascript %}
// 8~30자리, 숫자 포함, 영문자 포함, 특수문자 포함
/^.*(?=^.{8,30}$)(?=.*[0-9])(?=.*[a-zA-Z])(?=.*[!@#$%^&*_\)\(+=|}{\]\[\'\":;><.,?/]).*$/;
{% endhighlight %} 


### float 숫자
{% highlight javascript %}
/(?:\d*\.)?\d+/;
{% endhighlight %}

