---
title:  "type detection in javascript"
date:   2015-03-16 18:45:35
categories: javascript
---


### 배열 감지  

Array 타입인지 체크하는 방법으로 아래처럼 하면 안된다.
{% highlight javascript %}
var arr = [1, 2, 3];
alert(arr instanceof Array); // true
{% endhighlight %}

보통의 경우 위의 코드는 문제가 없다. 하지만 위의 검사는 전역 스코프에 존재하는 Array 생성자를 통해 생성되었다는 사실만 말해준다. 정확히는 위의 Array는 window의 프로퍼티이다. 다른 프레임에서 생성된 배열이면 이 코드는 false를 반환한다.
  
  
따라서 Array 검사는 아래와 같이 하자. 
{% highlight javascript %}
if( Object.prototype.toString.call( someVar ) === '[object Array]' ) {
  alert( 'Array!' );
}
{% endhighlight %}
  

이런식으로 `isArray()`, `isFunction()`, `isRegExp()` 함수를 만들어보겠다.

{% highlight javascript %}
function isArray(value) {
  return Object.prototype.toString.call(value) == "[object Array]";
}

function isFunction(value) {
  return Object.prototype.toString.call(value) == "[object Function]";
}

function isRegExp(value) {
  return Object.prototype.toString.call(value) == "[object RegExp]";
}
{% endhighlight %}


### Typeof operator  
  
javascript에는 몇 가지 primitive 타입이 있다.  
`null`, `undefined`, `number`, `boolean`, `string` 을 제외한 모든 값은 `object`이다. `function`과 `array`도 `object`이다.  
  

{% highlight javascript %}
// operator style
typeof x

// function-like
typeof(x)
{% endhighlight %}
  
연산자처럼 괄호 없이 써도 되고, 함수처럼 괄호를 써도 된다. 둘 다 똑같다.  
  
  
다음의 경우 주의해야한다.
{% highlight javascript %}
typeof null  // "object" 
typeof function(){} // "function" 
typeof NaN  // "number"
{% endhighlight %}
`typeof null`은 `object`가 나온다. `typeof`로 변수값을 검사했어도 안전하지 않을 수 있다.  
`NaN`이 `'Not-A-Number'`라는 점을 생각한다면, `typeof NaN`도 결과가 아이러니하다.  
  

`typeof`는 `null`을 제외한, primitive 타입을 구별할 때만 유용하다. `object` 타입에선 무의미하다.  
  

{% highlight javascript %}
// bad
if (typeof(jQuery) !== 'undefined') {
  // ...
}

// bad
if (jQuery) {
  // ...
}

// good
if (window.jQuery !== undefined) {
  // ...
}

// bad
if (window.jQuery) {
  // ...
}
{% endhighlight %}
변수의 유무를 확인할 땐 `typeof`를 사용하지 않는 것이 좋다.
  

### [[Class]]
{% highlight javascript %}
// Object is object
alert( typeof {key:'val'} );

// Array is object
alert( typeof [1, 2] );

// Date is object
alert( typeof new Date );
{% endhighlight %}
위 세 경우 모두 `object`로 나온다. 각각의 객체를 구분하고 싶은 경우 내장 객체의 `[[Class]]`를 이용하면 된다. `[[Class]]`를 직접 접근할 순 없다. `{}.toString()`을 이용해서 객체를 구분할 수 있다.  

{% highlight javascript %}
var toClass = {}.toString;

alert( toClass.call( [1,2] ) );		// [object Array]
alert( toClass.call( new Date ) );	// [object Date]
{% endhighlight %}


primitive 타입에서도 잘 작동한다.
{% highlight javascript %}
alert( {}.toString.call( 123 ) );		// [object Number]
alert( {}.toString.call( "hello" ) );	// [object String]
{% endhighlight %}
  

하지만 사용자가 만든 객체에선 단순히 `Object`로 나온다.
{% highlight javascript %}
function Animal(name) {
	this.name = name;
}

var animal = new Animal("Cat");

alert( {}.toString.call(animal) );		// [object Object]
{% endhighlight %}
  
네이티브 객체에서만 유용한 방법치다.  
  
대신 사용자가 만든 객체는 `instanceof`를 사용하자.
  


### 결론
네이티브 객체는 `{}.toString.call()`를 사용하고,  
사용자 객체는 `instanceof`를 사용하자.
`typeof`는 primitive 타입이나 function 타입에만 유용하다. 객체나 `null`에는 사용하지 말자.


 

참고자료

> - [http://javascript.info/tutorial/type-detection](http://javascript.info/tutorial/type-detection)  
  
