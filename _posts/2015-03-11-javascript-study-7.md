---
title:  "javascript 스터디 #7"
date:   2015-03-11 10:03:07
categories: javascript
---


### 재귀
  
재귀함수의 단골손님인 factorial 함수를 만들면 다음과 같다.
  
{% highlight javascript %}
// 재귀 함수
function factorial(num) {
  if( num <= 1 ){
    return 1;
  } else {
    return num * factorial(num-1);
  }
}
{% endhighlight %}
  
위처럼 쓰면 다음과 같은 문제점이 있다.
  
{% highlight javascript %}
var anotherFactorial = factorial;
factorial = null;
var result = anotherFactorial(10);  // 에러
{% endhighlight %}
  
재귀 함수 내에 함수 이름을 그래도 사용하여 의존성이 생겼다. 이를 대신하는 방법이 `arguments`의 `callee`를 이용하는 것이다.
  
{% highlight javascript %}
// arguments의 callee를 이용
function factorial(num) {
  if( num <= 1 ){
    return 1;
  } else {
    return num * arguments.callee(num-1);
  }
}
{% endhighlight %}
  
스트릭트 모드에선 `arguments.callee`를 쓸 수 없으니 `'이름 붙은 함수 표현식'`을 사용하자.
  
{% highlight javascript %}
// 이름 붙은 함수 표현식 사용
var factorial = (function f(num) {
  if( num <= 1 ){
    return 1;
  } else {
    return num * f(num-1);
  }
});
{% endhighlight %}

  
  

{% highlight javascript %}
function createComparisonFunction(propertyName) {
  return function(object1, object2){ {
    var value1 = object1[propertyName];
    var value2 = object2[propertyName];
    if( value1 < value2 ) {
      return -1;
    } else if( value1 > value2 ) {
      return 1;
    } else {
      return 0;
    }
  };
}
{% endhighlight %}
  
