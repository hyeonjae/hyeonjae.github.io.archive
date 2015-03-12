---
title:  "javascript 스터디 #6"
date:   2015-03-09 19:06:18
categories: javascript
---

1. primitive 타입
{% highlight javascript %}
var s = "Hello";
s.name = "Joe";
alert(s.name);
{% endhighlight %}
s는 primitive 타입이다. 두번째 줄에서 `name`이라는 프로퍼티에 접근하려 한다. 하지만 primitive 타입에는 해당 프로퍼티가 없다. 자바스크립트 엔진은 `s.name`의 순간에 `s`를 `object`타입으로 형변환한 객체를 생성한다. 그리고 `name`프로퍼티를 만들어서 "Joe"를 할당한다. 그리고 `s.name = "Joe";` 문장이 끝나는 순간 임시로 생성했던 `object` 객체를 파괴한다.  
그렇기 때문에 세번째줄 `alert(s.name);`은 `undefined`가 출력된다.  
  
2. new와 생성자 함수
{% highlight javascript %}
var person1 = new Person();
var person2 = Person();
{% endhighlight %}
자바스크립트에는 클래스가 없다. 오로지 함수만을 이용하여 객체를 관리한다. 위 두 코드는 거의 같은 의미지만 내부는 약간 다르게 동작한다.

`new`의 동작에 대한 stackoverflow의 설명이다.

1. Initializes a new native object
2. Sets the internal [[Prototype]] of this object, pointing to the Function prototype property.
    If the function's prototype property is not an object (a primitive values, such as a Number, String, Boolean, Undefined or Null), Object.prototype is used instead.
3. After creating the object, it calls the function, providing the object as its this value.
4. If the return value of the called function, is a primitive, the object created internally is returned.
5. Otherwise, if an object is returned, the object created internally is lost.


{% highlight javascript %}
Person() {
  this.name = "Jack";
}

var p1 = new Person();
{% endhighlight %}
위에서 `new Person();` 의 과정을 슈도 코드로 설명하면 다음과 같다.

{% highlight javascript %}
// 빈 객체를 생성한다.
var obj = {};

// 빈 객체의 내부 [[prototype]] 포인터를
// 생성자(Person)의 프로토타입을 가리키도록 한다.
// 만약 생성자(Person)의 프로토타입이 primitive type이면
// Object의 프로토타입을 가리키도록 한다.
obj.[[prototype]] = (typeof Person.prototype !== 'object')
    ? Object.prototype : Person.prototype;

// this를 obj로 넣어준다.
var r = Person.call(obj);

// 생성자(Person)가 리턴한 값을 리턴하면 끝
// 만약 생성자(Person)이 객체가 아닌 값을 리턴했으면
// 위에서 생성한 임시 객체(obj)를 리턴한다.
if( typeof r === 'object' ) {
  obj = null
  return r;
} else {
  return obj;
}
{% endhighlight %}