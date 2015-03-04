---
title:  "javascript 스터디 #1"
date:   2015-03-04 11:00:00
categories: javascript
---

## <script>

 - async: 비동기로 스크립트를 다운받음. 잘 안쓴다.
 - defer: 잘 안쓴다.
 - charset: 브라우저가 이 속성을 무시하는 경우가 많음.
 - language: 폐기됨.
 - src: 실행할 코드의 위치. src속성이 있으면 `<script>`와 `</script>`사이의 코드는 무시함.
 - type: 원래는 안 쓰는게 맞는데, 브라우저 호환을 위해 습관적으로 씀.

인라인 스크립트 방식
{% highlight javascript %}
<script type="text/javascript">
function hello(){
  alert("hello");
}
</script>
{% endhighlight %}

외부 파일
{% highlight javascript %}
<script type="text/javascript" src="common.js"></script>
{% endhighlight %}


아래와 같이 문자열에 `</script>`가 있으면 안됨.
{% highlight javascript %}
<script type="text/javascript">
function hello(){
  alert("</script>");
}
</script>
{% endhighlight %}

문자열에 `</script>`를 사용하려면 `/`에 `\`를 사용하자

{% highlight javascript %}
<script type="text/javascript">
function hello(){
  alert("<\/script>");
}
</script>
{% endhighlight %}

다음과 같이 쓰는 방식은 XHTML에서만 되고, HTML에선 허용하지 않는다. 일부 브라우저(IE 등)에서도 지원하지 않는다.
{% highlight javascript %}
<script type="text/javascript" src="common.js" />
{% endhighlight %}

따라서 꼭 `</script>`를 사용하자.

---

## 스크립트 처리 지연

`<script>`는 `<head>`안에 넣는 것이 일반적이었다.

{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <title>Hello world</title>
    <script type="text/javascript" src="common.js"></script>
    <script type="text/javascript" src="example.js"></script>
  </head>
  <body>
    <!-- Page content -->
  </body>
</html>
{% endhighlight %}
이럴 경우 외부 자바스크립트 파일을 내려받고, 파싱하고 모두 해석하고 나서야 페이지 렌더링을 시작한다. 

외부 자바스크립트 파일이 클 경우, 페이지 렌더링이 지연되어 텅 빈 화면이 오래 보인다. 그래서 요즘은 일반적으로 `<body>` 안의 맨 마지막에 `<script>`를 넣는다.
{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <title>Hello world</title>
  </head>
  <body>
    <!-- Page content -->
    <script type="text/javascript" src="common.js"></script>
    <script type="text/javascript" src="example.js"></script>
  </body>
</html>
{% endhighlight %}

이렇게 되면 브라우저는 `<body>`를 읽는 순간 페이지 렌더링을 시작하고, 그 후에 자바스크립트 코드를 처리하므로, 사용자는 페이지가 조금 더 빠르게 시작된다고 느끼게 된다.

HTML5부터는 `defer`를 이용하여 아래와 같은 방식으로 스크립트 지연을 지원한다.
{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <title>Hello world</title>
    <script type="text/javascript" defer="defer" src="common.js"></script>
    <script type="text/javascript" defer="defer" src="example.js"></script>
  </head>
  <body>
    <!-- Page content -->
  </body>
</html>
{% endhighlight %}

비슷하게 `async`도 있다. 실행 순서를 지켜주지 않는다는 점 외에는 `defer`와 같다.

---

## XHTML
XHTML에서는 `<script>`를 특별하게 취급하지 않는다. 따라서 `<script>` 내부의 `<`를 태그로 간주한다.

{% highlight html %}
<script type="text/javascript">
function compare(a, b){
    if (a < b) {
        alert("A is less than B");
    }
}
{% endhighlight %}

`<`를 태그로 인식하는 문제를 해결하는 방법은 여러가지가 있다. 

html 엔티티로 바꾸는 방법이다. 이 방법은 가독성이 떨어진다.
{% highlight html %}
<script type="text/javascript">
function compare(a, b){
    if (a &lt b) {
        alert("A is less than B");
    }
}
{% endhighlight %}

다른 방법으로는 CDATA 섹션으로 감싸는 방법이다. 
{% highlight html %}
<script type="text/javascript">
<![CDATA[
function compare(a, b){
    if (a &lt b) {
        alert("A is less than B");
    }
}
]]>
{% endhighlight %}

일부 CDATA 섹션을 지원하지 않는 브라우저를 위해 주석으로 처리한다.

{% highlight html %}
<script type="text/javascript">
//<![CDATA[
function compare(a, b){
    if (a &lt b) {
        alert("A is less than B");
    }
}
//]]>
{% endhighlight %}
