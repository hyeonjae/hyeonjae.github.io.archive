---
title:  "javascript arguments"
date:   2015-03-05 23:00:00
categories: javascript
---



## arguments에 대해



{% highlight javascript %}
function sum(num1, num2) {
    debugger;
    return num1+num2;
}
sum(1,2);
{% endhighlight %}
위의 코드를 디버깅한 모습이다. 
  
![1]({{ site.url }}/assets/sum_arguments.png)
  
`sum`함수 내부에 `arguments` 객체가 있다. 이 `arguments`가 함수의 매개변수에 대한 정보를 가지고 있다. 마치 배열처럼 index로 접근할 수도 있고, length 프로퍼티로 매개변수의 갯수를 알 수도 있다.


자바스크립트는 함수의 매개변수에 엄격하지 않다. 타입 검사도 안하고, 호출시 매개변수 갯수가 맞지 않아도 된다. 맞지 않는 매개변수는 undefined가 된다.
{% highlight javascript %}
function go(a, b) {
  alert("a="+a+", b="+b);
}

go(1);     // a=1, b=undefined
go(1,2);   // a=1, b=2
go(1,2,3); // a=1, b=2, extra argument is not listed
{% endhighlight %}

함수는 매개변수를 기대하지만, 아무것도 넘기지 않은 경우, 해당 매개변수는 undefined이다. 
{% highlight javascript %}
function check(x) {
  alert(x === undefined); // ok now I know there is no x
}

check()
{% endhighlight %}

많은 프로그래밍 언어에서 아래와 같이 함수 이름은 같지만, 매개변수가 다른 경우, 컴파일러가 적절한 함수를 찾아 호출해준다. 객체지향의 다형성을 갖는 중요한 기능이다. 
{% highlight javascript %}
function log(a) {
  return "first";
}

function log(a,b,c) {
  return "second";
}

log(a); // "first"
log(a,b,c); // "first"
{% endhighlight %}
하지만 자바스크립트에선 함수 이름이 같으면, 아무리 매개변수가 달라도 가장 마지막에 선언한 함수 한개만 유효하다. 자바스크립트는 함수 선언시 매개변수를 바탕으로 시그니처를 만들지 않는다.


#### 이름 없는 매개변수 사용하기
함수 선언시 매개변수를 명시하지 않았더라도, `arguments` 객체를 통해 실제 넘어온 매개변수에 접근할 수 있다. 
{% highlight javascript %}
function sayHi() {
  for(var i=0; i &lt; arguments.length; i++) {
    alert("Hi, " + arguments[i]);
  }
}
sayHi("Cat", "Alice");  // 'Hi, Cat' and 'Hi, Alice'
{% endhighlight %}

`arguments`는 배열처럼 인덱스 접근이 가능하지만 실제 배열은 아니다.
{% highlight javascript %}
function sayHi() {
  var a = arguments.shift(); // error! arguments is not Array
  alert(a);
}

sayHi();
{% endhighlight %}
`arguments`가 배열이라면 `shift()` 프로퍼티가 있어야 한다. 하지만 배열이 아니기 때문에 에러난다.

`arguments`가 무엇인지 알아보자.
{% highlight javascript %}
(function() {
  alert( {}.toString.call(arguments) );  // [object Arguments] 
})();
{% endhighlight %}
위의 코드는 일종의 idiom이다. 다음에 자세히 다루겠다.
  
실행해보면 `arguments`는 `Arguments` 객체라는 것을 알 수 있다.

#### `arguments`는 배열과 비슷하다.
아래 코드를 실행시켜보자.
{% highlight javascript %}
function sayHi() {
  var args = [].join.call(arguments, ':');
  alert(args);  // 1:2:3
}

sayHi(1,2,3);
{% endhighlight %}
`arguments`는 배열도 아닌데 `join()`함수로 문자열을 만들었다. 참고로 `join()`함수는 배열을 문자열로 만드는 함수이다. `arguments`는 index로 원소에 접근할 수 있고, `length`프로퍼티가 잘 정의되어 있어서 마치 배열인 것처럼 사용할 수 있는 것이다. 
  
어떤 객체라도 index접근과 `length`프로퍼티를 잘 정의해준다면, `join()`함수에서 사용할 수 있다.
{% highlight javascript %}
var obj = { 0: "A", 1: "B", length: 2 }

alert( [].join.call(obj) ) // "A,B"
{% endhighlight %}
위 코드는 임의의 obj 객체에 `length`프로퍼티를 정의하고, index로 접근할 수 있도록 0과 1의 프로퍼티를 만들었다. `length`가 2이므로 1까지 프로퍼티만 만들었다. 역시 `join()`이 잘 작동한다.

#### `arguments`로 진짜 배열을 만들어보자.
배열의 `slice()`메서드를 매개변수 없이 사용하면, 0부터 length까지 복사한다.
{% highlight javascript %}
function sayHi() {
  var args = [].slice.call(arguments); // slice without parameters copies all

  alert( args.join(':') ); // now .join works
}

sayHi(1,2);
{% endhighlight %}
`arguments`객체를 파라미터 없는 `slice()`메서드를 이용해서 배열로 만들었다.

#### 디폴트 파라미터
자바스크립트에서 몇가지 트릭으로 디폴트 파라미터를 구현 할 수 있다.
  
`undefined`를 이용하는 방법
{% highlight javascript %}
function sayHi(who) {
  if (who === undefined) who = 'me'; 
    
  alert(who) ;
}
{% endhighlight %}
  
OR `||` 연산자를 이용하는 방법
{% highlight javascript %}
function sayHi(who) {
  who = who || 'me';  // if who is falsy, returns 'me'
    
  alert(who);  
}
sayHi("John");      // 'John'
sayHi();  // 'me'
{% endhighlight %}
  

#### keyword arguments

아래와 같은 여러 파라미터를 입력 받는 함수가 있다.
{% highlight javascript %}
function showWarning(width, height, title, contents, showYesNo) {
  width = width || 200; // default values
  height = height || 100;
  
  var title = title || "Warning";

  // do something
}
{% endhighlight %}
각 파라미터를 디폴트값을 지정해주고 있다.

디폴트값을 잘 지정해주고 있기 때문에, 값을 넣어도 되고 안 넣어도 된다.
쓰기 편리해 보인다.
{% highlight javascript %}
showWarning(null, null, null, "Warning text", true);

showWarning(200, 300, null, "Warning text");
{% endhighlight %}

하지만 문제점이 있다. 파라미터가 많아진 경우, 이 함수를 사용하는 사람이 순서를 잘못지켜 엉뚱한 값을 대입할 수 있다. 파이썬이나 루비 같은 다른 프로그래밍 언어에서 keyword parameters를 지원한다. 자바스크립트에서도 객체를 통해 구현할 수 있다.

우선 파라미터를 개별적으로 받지 말고, 하나의 객체로 받자.
{% highlight javascript %}
function showWarning(options) {
  var width = options.width || 200  // defaults 
  var height = options.height || 100
  
  var title = options.title || "Warning"

  // ...
}
{% endhighlight %}

그리고 프로퍼티로 접근하도록 객체를 넘겨주자.
{% highlight javascript %}
showWarning({ 
  contents: "Text", 
  showYesNo: true
});
{% endhighlight %}
파라미터를 이름으로 명확하게 지정할 수 있기 때문에, 실수할 가능성이 줄어들었다.
장점은 또 있다. 파라미터를 재활용할 수 있다.

{% highlight javascript %}
var opts = {
  width: 400,
  height: 200, 
  contents: "Text", 
  showYesNo: true
};

showWarning(opts);

opts.contents = "Another text";
showWarning(opts); // another text with same options
{% endhighlight %}
한번 `opts`를 사용하고, 일부 프로퍼티 값만 바꿔서 다시 사용한다.
  
keyword arguments는 많이 사용되는 기법이다.
  
#### callee

`arguments.callee`는 실행된 함수를 가리킨다. 아래와 같이 이름 없는 함수내부에서 함수 자체를 가리킬 수 있다.
{% highlight javascript %}
var func = function(n) {  
  return n==1 ? 1 : n*arguments.callee(n-1);
}
{% endhighlight %}
나중에 `func`가 바뀌더라도 재귀함수는 잘 작동한다.
  
#### caller

`arguments.callee`가 실행된 함수 자체였다면, `arguments.callee.caller`는 실행된 함수를 실행시킨 함수를 가리킨다.
{% highlight javascript %}
f();

function f() {
  alert(arguments.callee.caller); // undefined
  g();
}

function g() {
  alert(arguments.callee.caller); // f
}
{% endhighlight %}

#### callee, caller

`arguments.callee`는 ECMA-262에서 사용하지 않게 되었다. 사실 callee를 쓸 일이 거의 없으므로 사용하지 말자.
`arguments.caller`와 `arguments.callee.caller`는 같은 의미다. 하지만 많은 브라우저에서 `arguments.caller`를 지원하지 않는다. 따라서 caller를 쓸 일이 있다면 `arguments.callee.caller`를 사용하자.


참고자료

 > - [http://javascript.info/tutorial/arguments](http://javascript.info/tutorial/arguments)  