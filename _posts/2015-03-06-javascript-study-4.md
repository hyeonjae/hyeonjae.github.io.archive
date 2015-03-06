---
title:  "javascript 스터디 #4"
date:   2015-03-06 16:42:45
categories: javascript
---

### 팩토리 패턴

{% highlight javascript %}
function createPerson(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    }
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
{% endhighlight %}
  
단점
 - 생성한 객체가 어떤 타입인지 알 수 없다. 오로지 Object 타입으로만 인식
 
  
### 생성자 패턴
{% highlight javascript %}
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        alert(this.name);
    }
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
{% endhighlight %} 
  
특징
 - `new` 를 사용하였다.
 - 보통 생성자 함수는 첫 글자를 대문자로 표기하는 것이 관례이다. 
  
  
`new`가 하는 일은 다음과 같다.
1. 내부적으로 `new Object`로 객체를 생성한다.
2. 생성자의 `this`에 방금 생성한 객체를 할당한다.
3. 생성자 내부 코드를 실행한다.
4. 새 객체를 반환한다.

장점
 - 생성자 패턴으로 만든 객체는 `instanceof`로 타입 검사를 할 수 있다.
{% highlight javascript %}
if( person1 instanceof Object ) {
    // do sometheing
}

if( person1 instanceof Person ) {
    // do sometheing
}
{% endhighlight %}
`person1`은 `Object`의 객체이기도 하면서 `Person`의 객체이다.
  
  
단점
 - 인스턴스마다 메서드가 생긴다.

`person1.sayName`과 `person2.sayName`는 서로 다른 함수로 인식한다.  
함수 정의를 생성자 밖으로 내보내면 서로 같은 함수로 인식한다.
{% highlight javascript %}
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName() {
    alert(this.name);
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
{% endhighlight %} 
  
이 경우 `Person`의 `sayName`는 외부의 `sayName()`함수를 가리켜 공유한다. 하지만 전역에 함수가 할당되어 좋은 코드는 아니다.

### 프로토타입 패턴

> 모든 함수는 prototype 프로퍼티를 가진다.  
  
prototype의 프로퍼티와 메서드는 객체 인스턴스 전체에서 공유된다. 
{% highlight javascript %}
function Person() {
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person1 = new Person();
person1.sayName();

var person2 = new Person();
person2.sayName();
{% endhighlight %}
위의 경우 `person1.sayName`과 `person2.sayName`는 완전히 같다. `Person.prototype.sayName`을 가리킨다.

