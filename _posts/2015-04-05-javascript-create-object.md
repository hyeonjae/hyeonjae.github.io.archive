---
title:  "javascript 객체 생성 방법"
date:   2015-04-05 21:09:50
categories: javascript
---

# 팩토리 패턴
{% highlight javascript %}
function createPerson(name, age, job) {
  var o = {};
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function () {
    alert('name: ' + this.name);
  }
  return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Creg", 28, "Doctor");
{% endhighlight %}

특징
 - new 없이 객체 생성할 수 있음
 - 메소드가 객체마다 별도로 생성됨


# 생성자 패턴
{% highlight javascript %}
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function () {
    alert('name: ' + this.name);
  }
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Creg", 28, "Doctor");
{% endhighlight %}

특징
 - new로 객체를 생성 (instanceof 시 Person으로 나옴)
 - 메소드가 객체마다 별도로 생성됨

# 생성자 패턴 + 프로토타입 패턴
{% highlight javascript %}
function Person(name, age, job) {
  this.name = name
  this.age = age;
  this.job = job;
}
Person.prototype = {
  constructor: Person,
  sayName: function () {
    alert('name: ' + this.name);
  }
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
{% endhighlight %}

특징
 - new로 객체를 생성 (instanceof 시 Person으로 나옴)
 - constructor 프로퍼티를 명시적으로 생성
 - 메소드 생성 부분이 function 외부에 있음
  
  
# 동적 프로토타입 패턴
{% highlight javascript %}
function Person(name, age, job) {
  this.name = name
  this.age = age;
  this.job = job;

  if (typeof this.sayName != "function") {
    Person.protytype.sayName = function () {
      alert('name: ' + this.name);
    }
  }
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
{% endhighlight %}

특징
 - 완벽한 캡슐화
 - 메소드도 공유
 - instanceof로 객체 확인 가능
