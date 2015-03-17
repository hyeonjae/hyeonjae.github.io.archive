---
title:  "innerHTML 팁"
date:   2015-03-17 10:57:03
categories: javascript
---


{% highlight javascript %}
// innerHTML을 여러번 변경
for (var i=0, len=values.length; i < len; i++) {
  ul.innerHTML += "<li>" + values[i] + "</li>";
}
{% endhighlight %}
위의 코드는 `for` 루프마다 `innerHtml`을 변경한다. 매번 reflow가 발생해서 비효율적이다. 이런 경우 임시 변수에 변경할 html을 만들어 두고 마지막 한번만 `innerHtml`을 변경해주면 된다.  
  
  
{% highlight javascript %}
// innerHTML을 한번만 변경
var itemsHtml = "";
for (var i=0, len=values.length; i < len; i++) {
  itemsHtml += "<li>" + values[i] + "</li>";
}
ul.innerHTML = itemsHtml;
{% endhighlight %}  
  

