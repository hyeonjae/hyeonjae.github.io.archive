---
title: 자주 쓰는 정규식 모음
date: 2015-03-25 15:29:10
categories: regex
---
  
### 최소매칭

정규식으로 문자열만 매칭하고 싶은 경우 `/".*"/` 로 생각하기 쉽다.
{% highlight c %}
printf("hello %s", "Kim");
{% endhighlight %}  
  
하지만 위의 경우 `/".*"/`로 매칭할 경우 `"hello %s"`이 아닌 `"hello %s", "Kim"`이 매칭된다.  
`.*`이 부분이 `"`까지 포함하기 때문이다. 보통 `*`나 `+`는 최대 매칭을 기본으로 한다.  
`"hello %s"`만 매칭되는 것을 의도했으니, 정규식을 고쳐보면 다음과 같다.  
  
{% highlight javascript %}
/".*?"/
{% endhighlight %}  
  
`.*`대신 `.*?`로 할 경우 최소 매칭하게 된다.
  
  
### lookaround
  
{% highlight text %}
(?=(regex)) - positive lookahead
(?!(regex)) - negative lookahead
(?<=(regex)) - positive lookbehind
(?<!(regex)) - negative lookbehind
{% endhighlight %}
자바스크립트에선 lookbehind 기능을 지원하지 않는다.  

lookahead는 앞의 정규식 조건에 추가 조건을 기술할 수 있다. 앞의 정규식 조건에 매칭되면, 그 이후의 입력 문자열을 미리 매칭시켜본다.
{% highlight text %}
/^.*
    (?=^.{8,30}$)
    (?=.*[0-9])
    (?=.*[a-zA-Z])
    (?=.*[!@#$%^&*_\)\(+=|}{\]\[\'\":;><.,?/])
    (?!.*(?:hello|welcome).*)
.*$/
{% endhighlight %}
유효한 비밀번호 매칭하는 정규식이다. `.*`에 `(?=^.{8,30}$)`조건을 추가로 기술한다. 모든 문자에 매칭되는데, 거기에 `.{8,30}`조건을 추가한다. 길이가 8~30 이고, `[0-9]`가 있어야하고, `[a-zA-Z]`도 있어야 하고, 특수문자도 포함되어야 한다.  
거기에 negative lookahead 조건으로 특정 단어가 포함되지 않는 조건을 추가하였다.  


최소 매칭, lookahead, capture 정도만 알면 정규식을 잘 알고 있다고 볼 수 있다.  
  


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
