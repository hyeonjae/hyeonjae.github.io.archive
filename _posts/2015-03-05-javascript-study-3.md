---
title:  "javascript 스터디 #3"
date:   2015-03-05 17:00:00
categories: javascript
---

## 객체
객체를 생성하는 방법은 두가지가 있다. 첫번째는 Object의 생성자를 이용하는 것이다.
{% highlight javascript %}
var person = new Object();
person.name = "Nicholas";
person.age = 29;
{% endhighlight %}

두번째 방법은 `'객체 리터럴'` 표기법이다.
{% highlight javascript %}
var person = {
  name: "Nicholas",
  age: 29  
};
{% endhighlight %}
`'객체 리터럴'`방식에서 중괄호 {} 뒤에 `;`가 붙은 점을 유의하자.

`'꼬리 쉼표'`는 없어야 한다.
  
![1]({{ site.url }}/assets/json-tailing-comma.png)
  
![2]({{ site.url }}/assets/json-object-specification.png)


---


## 객체 프로퍼티 접근

역시 두가지 방식이 있다.
{% highlight javascript %}
// 대괄호 표기법
person["name"];

// 점 표기법
person.name;
{% endhighlight %}
보통은 `'점 표기법'`을 이용하고, 아래와 같이 `'점 표기법'`으로 쓸 수 없는 경우 `'대괄호 표기법'`을 이용하자. 

{% highlight javascript %}
// 대괄호 표기법
person["first name"];

// 점 표기법
//person.first name; ??
{% endhighlight %}
프로퍼티 이름이 문법 에러를 일으키는(위의 경우 공백 문자) 문자가 있거나, 예약어인 경우 대괄호 표기법을 이용하자.


---


## Array
Array 객체를 생성하는 법
  
  
생성자를 이용
{% highlight javascript %}
var colors = new Array(20);
var colors = new Array("red", "green", "blue");
var colors = Array(20)      // new 생략 okay
{% endhighlight %}
  
`'배열 리터럴'`이용
{% highlight javascript %}
var colors = [];
var colors = ["red", "green", "blue"];
{% endhighlight %}
  
문제를 일으킬 수 있는 배열
{% highlight javascript %}
var colors = [,,,];
var colors = ["red", "green", "blue", ];
{% endhighlight %}
보통 빈 곳은 `undefined`으로 채운다. 하지만 마지막 콤마(,) 다음의 공간을 채울지 말지는 브라우저마다 해석이 다르다. 모호한 경우 사용하지 말자.

배열의 length 프로퍼티 값을 변경하면 배열의 실제 사이즈도 변한다. 추가된 공간은 `undefined`로 채워진다.
{% highlight javascript %}
var colors = ["red", "green", "blue"];
colors.length = 5;
{% endhighlight %}
  
![3]({{ site.url }}/assets/array_img.png)
  
  
---


## 배열 감지
  
안전한 배열 타입 감지는 `instanceof`를 사용하지 말고 `isArray()`를 사용해라.
{% highlight javascript %}
if (value instanceof Array) {
    // do something
}
{% endhighlight %}
{% highlight javascript %}
if (Array.isArray(value)) {
    // do something
}
{% endhighlight %}

---

## 스택
{% highlight javascript %}
var stack = new Array();

// push
stack.push("red");
stack.push("green", "blue");

// pop
var item = stack.pop();
{% endhighlight %}

## 큐
{% highlight javascript %}
var queue = new Array();

// push
queue.push("red");
queue.push("green", "blue");

// pop
var item = queue.shift();
{% endhighlight %}

![4]({{ site.url }}/assets/array_usage.png)

---


## 반복 메서드
  
 - `every()`
 - `filter()`
 - `forEach()`
 - `map()`
 - `some()`

생략


---

## 감소 메서드

 - `reduce()`
 - `reduceRight()`
  
생략

  
---
  
## Date
{% highlight javascript %}
var now1 = new Date(Date.UTC(2005, 3, 7, 17, 55, 55));
//now1: Fri Apr 08 2005 02:55:55 GMT+0900 (대한민국 표준시)

var now2 = new Date(2005, 3, 7, 17, 55, 55);
//now2: Thu Apr 07 2005 17:55:55 GMT+0900 (대한민국 표준시)
{% endhighlight %}

GMT+0900만큼 차이가 난다.

---

## RegExp

정규식은 별도의 스레드에서 자세히 다루겠다.


---

## Function

### 함수 선언 방법 및 차이
{% highlight javascript %}
// 방법1. 함수 선언
function sum (num1, num2) {
    return num1 + num2;
}

// 방법2. 함수 표현식
var sum = function(num1, num2) {
    return num1 + num2;
};
{% endhighlight %}

위 두 방식의 차이는 아래에서 나타난다.
{% highlight javascript %}
var result = sum(1, 2);

function sum (num1, num2) {
    return num1 + num2;
}
{% endhighlight %}
위의 경우, 함수 `sum`이 아래에 있더라도, 해당 컨텍스트가 사용 될 때, 선언된 함수가 호이스트(`hoist`)되어 접근할 수 있다.

{% highlight javascript %}
var result = sum(1, 2);

var sum = function(num1, num2) {
    return num1 + num2;
};
{% endhighlight %}
반면 함수 표현식으로 사용한 경우, `var sum`만 호이스트(`hoist`)되고, `sum`에는 `undefined`인 상태이다. `var sum = function(num1, num2)` 부분이 실행 될 때, `var sum`이 비로소 함수가 할당된다.


함수 이름은 함수를 가리키는 포인터일 뿐이므로, 함수를 가리키는 변수에 다른 값을 할당해도, 함수 객체 자체는 변하지 않는다.

  
매개변수와 함수 이름을 시그니처로 사용하지 않기 때문에, 오버로딩도 지원하지 않는다.



### 값 처럼 쓰는 함수
{% highlight javascript %}
function createComparisonFunction(propertyName) {
    return function(object1, obejct2) {
        var v1 = object1[propertyName];
        var v2 = object2[propertyName];
        
        if (v1 < v2 ) {
            return -1;
        } else if ( v1 > v2 ) {
            return 1;
        } else {
            return 0;
        }
    };
}

var data = [{name: "Zachary", age: 28}, {name: "Nicholas", age: 29}];

// 이름으로 정렬
data.sort(createComparisonFunction("name"));

// 나이로 정렬
data.sort(createComparisonFunction("age"));
{% endhighlight %}


### callee, caller
{% highlight javascript %}
function factorial(num) {
    if (num <= 1 ) {
        return 1;
    } else {
        return num * factorial(num-1);
    }
}
{% endhighlight %}
위와 같이 재귀 함수를 만들 경우, 함수 이름이 반드시 `factorial` 여야 재귀 호출이 보장된다.
{% highlight javascript %}
var oldFactorial = factorial;
factorial = null;

oldFactorial(10);
{% endhighlight %}
이런 경우, `factorial`이 `oldFactorial`로 바뀌었기 때문에, 함수 내부의 `factorial(num-1)`도 `factorial`이 아닌 `oldFactorial`이 호출되었으면 한다.

이럴 때, 아래와 같이 `callee`를 이용하자.
{% highlight javascript %}
function factorial(num) {
    if (num <= 1 ) {
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}
{% endhighlight %}
  
`caller`는 `callee`와 비슷하다.
{% highlight javascript %}
function outer() {
    inner();
}
function inner() {
    var parent = inner.caller;
}
{% endhighlight %}
`caller` 는 `outer()`이다.
{% highlight javascript %}
function outer() {
    inner();
}
function inner() {
    var parent = arguments.callee.caller;
}
{% endhighlight %}


`this`는 함수가 실행중인 컨텍스트 객체의 참조값이다.
  
전역 스코프에서 함수를 호출했다면, `this` 객체는 `window`를 가리키고, 다른 스코프에서 함수가 호출되었다면, `this` 객체는 해당 스코프를 가리키게 된다.

{% highlight javascript %}
window.color = "red"
var o = {color: "blue"};

function sayColor() {
    return this.color;
}

sayColor();         // "red", this는 window

o.sayColor = sayColor;
o.sayColor();       // "blue", this는 o
{% endhighlight %}


#### length, arguments, call, apply
`function.length`는 function 선언시의 argument의 갯수이고, `arguments.length`는 그 함수를 호출할 때 들어온 매개변수의 갯수이다.
  
함수 객체에서 함수를 호출하는 방법은 두 가지가 있다.

 - `call()`
 - `apply()`

두 메서드 모두 첫번째 인자로 `this`를 넘겨야 한다. `apply()`는 매개변수로 배열을 넘기면 되고, `call()`는 매개변수를 각각 넣어줘야 한다.
{% highlight javascript %}
function sum(num1, num2, num3){
    return num1, num2, num3;
}

sum.apply(this, [1, 2, 3]);
sum.call(this, 1, 2, 3);
{% endhighlight %}

### String 자주 쓰는 메서드 search, replace, split
  
search
{% highlight javascript %}
var text = "cat, bat, sat, fat";

var pos = text.search(/at/);
{% endhighlight %}
  
replace
{% highlight javascript %}
var text = "cat, bat, sat, fat";

var result = text.replace(/(.at)/g, "'$1'");
// "'cat', 'bat', 'sat', 'fat'";
{% endhighlight %}
  
replace 응용
{% highlight javascript %}
function htmlEscape(text){
    return text.replace(/[<>"&]/g, function(match, pos, originalText){
        switch(match){
            case "<":
                return "&lt;";
            case ">":
                return "&gt;";
            case "&":
                return "&amp;";
            case "\"":
                return "&quot;";
        }             
    });
}

alert(htmlEscape("<p class=\"greeting\">Hello world!</p>"));
//&lt;p class=&quot;greeting&quot;&gt;Hello world!&lt;/p&gt;
{% endhighlight %}
  
split

Array -> String: join
  
String -> Array: split

{% highlight javascript %}
var color = "red,orange,yellow,green,blue";

var result = color.split(",", 3);
// ["red", "orange", "yellow"]
{% endhighlight %}


### URI 인코딩 메서드

`encodeURI()`, `decodeURI()` 는 `:`, `/`', `?`, `#` 등 URI의 일부분으로 사용하는 특수문자는 인코딩 하지 않음
  
`encodeURIComponent()`, `decodeURIComponent()` 는 모든 특수문자를 인코딩함
{% highlight javascript %}
var uri = "http://www.wrox.com/illegal value.htm#start";

//"http://www.wrox.com/illegal%20value.htm#start"
var encodedUri = encodeURI(uri);
var decodedUri = decodeURI(encodedUri);

//"http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start"
var encodedUri = encodeURIComponent(uri)
var decodedUri = decodeURIComponent(encodedUri);
{% endhighlight %}