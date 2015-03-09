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

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
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

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
{% endhighlight %} 
  
이 경우 `Person`의 `sayName`는 외부의 `sayName()`함수를 가리켜 공유한다. 하지만 전역에 함수가 할당되어 좋은 코드는 아니다.

### 프로토타입 패턴

> 모든 함수는 prototype 프로퍼티를 가진다.  
  
prototype의 프로퍼티와 메서드는 객체 인스턴스 전체에서 공유된다. 
{% highlight javascript %}
function Person() {
    // 생성자 함수
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

각 인스턴스(`person1`, `person2`)는 내부에, `Person.prototype`을 가리키는 `[[Prototype]]` 포인터(브라우저마다 다르다. 파이어폭스, 사파리, 크롬은 `__proto__`라는 프로퍼티로 `[[Prototype]]`에 접근가능하다.)가 있다. 

![1]({{ site.url }}/assets/javascript_prototype.png)
  
유심히 살펴볼 점은, `Person` 생성자 함수와 `person1`, `person2` 인스턴스는 직접 연결되어 있지 않다. `person1`, `person2`는 `Person.prototype`와 연결되어 있고 각 프로퍼티를 공유한다.  
  
  
`__proto__`가 없는 브라우저도 `isPrototypeOf()` 메소드로 인스턴스와 `Prototype`의 연결 관계를 확인 할 수 있다.
{% highlight javascript %}
alert(Person.prototype.isPrototypeOf(person1));
alert(Person.prototype.isPrototypeOf(person2));
{% endhighlight %}

`person1.sayName()`을 호출할 경우, `person1`객체에 `sayName`이름의 함수가 있는지 찾는다. 없으면 `[[Prototype]]`포인터가 가리키는 곳에서 찾는다. 있으면 해당 함수를 호출한다.  
  
인스턴스에서 `prototype`은 읽기만 가능하고 수정은 불가능하다. 만약 인스턴스에서, `prototype`의 프로퍼티와 같은 이름의 프로퍼티를 추가하면 `prototype`에 추가되는 것이 아니라 해당 인스턴스에 추가된다.

{% highlight javascript %}
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person1 = new Person();
person1.job = "Doctor";     // Person.prototype.job와 같은 이름의 
                            // job 프로퍼티를 추가하면
                            // person1 인스턴스에 추가된다.
{% endhighlight %}
  
만약 `delete`로 `person1.job` 프로퍼티를 지우면, 다시 `person1.job`로 `prototype`의 `job`프로퍼티에 접근할 수 있다.

{% highlight javascript %}
person1.job = "Doctor";
alert(person1.job);     // "Doctor"

delete person1.job;
alert(person1.job);     // "Software Engineer", Person.prototype.job에 접근
{% endhighlight %}
  
`hasOwnProperty()`로 특정 프로퍼티가 인스턴스에 존재하는지, `prototype`에 존재하는지 검사할 수 있는 함수가 있다.

{% highlight javascript %}
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person1 = new Person();
alert(person1.hasOwnProperty("job"));   // "job"는 "prototype"에 있으므로 false

person1.job = "Doctor";
alert(person1.hasOwnProperty("job"));   // "job"는 인스턴스에 있으므로 true

delete person1.job;
alert(person1.hasOwnProperty("job"));   // "job"는 "prototype"에 있으므로 false
person1.job = "Doctor"; 
{% endhighlight %}
  
### in 연산자
  
`in` 연산자는 프로퍼티가 `prototype`이든 인스턴스든 있는지 검사한다. 
이를 응용해서 아래와 같이 인스턴스에는 없고 `prototype`에만 있는 경우를 검사할 수 있다.
  
{% highlight javascript %}
function hasPrototypeProperty(object, name){
    return !object.hasOwnProperty(name) && (name in object);
}
{% endhighlight %}

1. `prototype`에도 있고, 인스턴스에도 있는 경우
 - `hasOwnProperty()`로 검사
2. `prototype`에만 있는 경우
 - `!object.hasOwnProperty(name) && (name in object)`로 검사
3. 아예 없는 경우
 - `in`연산자로 검사

### 느슨한 연결

{% highlight javascript %}
// 객체 생성
var person1 = new Person();

// 객체 생성 이후 prototype에 sayHi 함수 추가
Person.prototype.sayHi = function() {
    alert("Hi");
};

// 객체에서 sayHi 함수 호출
person1.sayHi();
{% endhighlight %}
  
  
![2]({{ site.url }}/assets/javascript_prototype_asign1.png)


{% highlight javascript %}
function Person() {}

// 객체 생성
var person1 = new Person();

// 객체 생성 이후 prototype에 sayName 함수 추가
Person.prototype = {
    constructor: Person,
    name: "Nicholas",
    age: 29,
    job: "Software Engineer",
    sayName: function() {
        alert(this.name);
    }
};

// 객체에서 sayName 함수 호출
person1.sayName();      // 에러
{% endhighlight %}
  
  
![3]({{ site.url }}/assets/javascript_prototype_asign2.png)