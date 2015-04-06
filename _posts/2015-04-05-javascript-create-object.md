---
title:  "javascript 다양한 객체 생성 방법"
date:   2015-04-06 21:09:50
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
 - 완벽한 캡슐화j
 - 메소드도 공유
 - instanceof로 객체 확인 가능


# 객체 리터럴과 비공개 멤버

아래 두 코드는 익명 즉시 실행 함수를 이용하여 클로저를 만든다.  

{% highlight javascript %}
var myobj;

(function () {
  var name = "Nicholas";

  myobj = {
    getName: function () {
      return name;
    }
  };
})();

myobj.getName();
{% endhighlight %}

{% highlight javascript %}
var myobj = (function () {
  var name = "Nicholas";

  return {
    getName: function () {
      return name;
    }
  };
})();

myobj.getName();
{% endhighlight %}

# 비공개 함수를 노출

다음은 익명 즉시 실행 함수 내에 비공개 함수를 만들고, 일부 함수를 노출시키는 방법이다.

{% highlight javascript %}
var myobj;

(function () {
  var name = "Nicholas";

  function foo() {
    alert('foo()');
  }
  function bar() {
    alert('bar()');
  }
  function baz() {
    alert('baz()');
  }

  myobj = {
    foo: foo,
    bar: bar,
    baz: baz
  };
})();

myobj.getName();
{% endhighlight %}

비슷한 방법으로 객체를 직접 리턴하는 방법도 있다.
{% highlight javascript %}
var myobj = (function () {
  var name = "Nicholas";

  return {
    foo: function () {
      alert('foo()');
    },
    bar: function () {
      alert('bar()');
    }.
    baz: function () {
      alert('baz()');
    }
  };
})();

myobj.getName();
{% endhighlight %}

일부 함수만 선택적으로 노출하고 싶은 경우 다음과 같은 방식을 사용하면 좋다.
{% highlight javascript %}
var myobj = (function () {
  var name = "Nicholas";

  function foo() {
    alert('foo()');
  }
  function bar() {
    alert('bar()');
  }
  function baz() {
    alert('baz()');
  }

  myobj = {
    bar: bar,
    baz: baz
  };
})();

myobj.getName();
{% endhighlight %}