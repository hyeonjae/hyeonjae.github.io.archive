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


