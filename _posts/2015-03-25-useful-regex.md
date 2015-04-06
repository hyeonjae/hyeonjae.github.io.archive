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
var pattern = /((?:\/\*(?:[^*]|(?:\*+[^*\/]))*\*+\/)|(?:\/\/.*))/g;
{% endhighlight %}

[http://blog.ostermiller.org/find-comment](http://blog.ostermiller.org/find-comment)




