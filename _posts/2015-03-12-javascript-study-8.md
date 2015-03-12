---
title:  "javascript 스터디 #8"
date:   2015-03-12 11:47:35
categories: javascript
---

함수를 만드는 방법은 두가지가 있다. 함수 선언과 함수 표현식을 이용하는 방법이다. 약간의 차이가 있다.
{% highlight javascript %}
function sayHello() {
  alert("hello");
}
var sayHello = function() {
  alert("hello");
};

sayHello();    // okay
function sayHello() {
  alert("hello");
}

sayHello();    // undefined
var sayHello = function() {
  alert("hello");
};
{% endhighlight %}
함수 선언은 해당 컨텍스트가 실행될 때, 미리 등록된다. 따라서 선언 이전이라도 사용할 수 있다. 이를 호이스팅(hoisting)이라 한다.  
하지만 함수 표현식은 해당 변수가 초기화 되기 이전엔 등록되지 않는다. `var sayHello;`자체는 호이스팅되어 그 전에도 접근할 수 있지만, 초기화는 해당라인이 실행될때만 이루어진다.  
  
아래와 같은 코드는 함수 선언을 잘못사용한 경우이다.
{% highlight javascript %}
if(condition) {
  function sayHello() {
    alert("hello");
  }
} else {
  function sayHello() {
    alert("HELLO");
  }
}
// 마지막 function sayHello()만 유효함
{% endhighlight %}
  
아래처럼 함수 표현식으로는 위의 문제를 해결할 수 있다. 하지만 함수 표현식과 함수 선언의 차이를 설명하는 예제일뿐, 좋은 방식은 아니다.
{% highlight javascript %}
var sayHello;
if(condition) {
  sayHello = function() {
    alert("hello");
  };
} else {
  sayHello = function() {
    alert("HELLO");
  };
}
{% endhighlight %}
  
  
---
  
  

선언 이후 실제 실행되는 순간에는 클로저 변수 `var i`는 10이므로 항상 10이 출력된다. 의도는 0~9가 출력되는 것이지만, `i`는 함수가 실제 실행되는 순간의 값이다.
{% highlight javascript %}
function createFunctions() {
  var result = [];
  
  for(var i = 0; i < 10; i++) {
    result[i] = function() {
        return i;
    };
  }
}
{% endhighlight %}

위의 코드를 바르게 사용하려면 아래처럼, 함수로 한번 감싸고, `i`를 전달해서 실행을 시켜준다. `num`은 파라미터로 넘어가고, `return num;`에 의해 클로저로 남게 된다.
{% highlight javascript %}
function createFunctions2() {
  var result = [];
  
  for(var i = 0; i < 10; i++) {
    result[i] = (function(num) {
        return function() {
            return num;
        };
    })(i);
  }
}
{% endhighlight %}
[결과 보기](http://fiddle.jshell.net/hyeonjae/5drfg791/)
  
  
---
  
  
  

{% highlight javascript %}
var name = "The window";

var object = {
    name: "My object",
    
    getNameFunc: function() {
        return function() {
            return this.name;
        };
    }
};

alert(object.getNameFunc()());      // "The window" 출력
{% endhighlight %}
`object.getNameFunc()`여기까지는 `this`가 `object`를 가리키지만, `object.getNameFunc()`에서 리턴 받은 함수를 실행시키는 것은 `window`이다. 따라서 이전에 `this`를 보관할 필요가 있다.


{% highlight javascript %}
var name = "The window";

var object = {
    name: "My object",
    
    getNameFunc: function() {
        var that = this;
        return function() {
            return that.name;
        };
    }
};

alert(object.getNameFunc()());      // "My object" 출력
{% endhighlight %}