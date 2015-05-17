---
title: 자주 쓰는 정규식 모음
date: {}
categories: regex
published: true
---

### 최소매칭

정규식으로 문자열만 매칭하고 싶은 경우 `/".*"/` 로 생각하기 쉽다.
```c
printf("hello %s", "Kim");
```
하지만 위의 경우 `/".*"/`로 매칭할 경우 `"hello %s"`이 아닌 `"hello %s", "Kim"`이 매칭된다.  
`.*`이 부분이 `"`까지 포함하기 때문이다. 보통 `*`나 `+`는 최대 매칭을 기본으로 한다.  
`"hello %s"`만 매칭되는 것을 의도했으니, 정규식을 고쳐보면 다음과 같다.
```
/".*?"/
```
`.*`대신 `.*?`로 할 경우 최소 매칭하게 된다.




# 유용한 정규식 모음

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
