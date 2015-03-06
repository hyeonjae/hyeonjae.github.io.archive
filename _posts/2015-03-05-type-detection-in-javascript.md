---
title:  "type detection in javascript"
date:   2015-03-05 22:20:06
categories: javascript
---

이 글은 [다른 사이트의 글](http://javascript.info/tutorial/type-detection)을 옮겨온 것이다.


Array 타입인지 체크하는 방법으로 아래처럼 하면 안된다.
{% highlight javascript %}
var arr = [1,2,3];
alert(arr instanceof Array); // true
{% endhighlight %}
위의 경우는 문제 없지만, Array가 다른 window에서 만들어지고, 지금 window로 넘겨졌다면, `instanceof`검사는 실패하게 된다. 이유는 각 window마다 object 계층을 따로 가지고 있기 때문이다.
  
따라서 Array 검사는 아래와 같이 하자. 
{% highlight javascript %}
if( Object.prototype.toString.call( someVar ) === '[object Array]' ) {
    alert( 'Array!' );
}
{% endhighlight %}


