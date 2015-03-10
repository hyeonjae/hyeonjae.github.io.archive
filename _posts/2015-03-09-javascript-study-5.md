---
title:  "javascript 스터디 #5"
date:   2015-03-10 12:26:20
categories: javascript
---

### 프로토타입 체인

{% highlight javascript %}
/*
 * 프로토타입 체인
 */
function SuperType() {
  this.age = 29;
}

SuperType.prototype.getSuperValue = function() {
  return this.age;
};

function SubType() {
  this.subAge = 27;
}

// SuperType을 상속
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
  return this.subAge;
};

var instance = new SubType();
alert(instance.getSuperValue());
{% endhighlight %}

<iframe src="//www.slideshare.net/slideshow/embed_code/45636139" width="476" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>



### 생성자 훔치기 기법
{% highlight javascript %}
function SuperType(name){
  this.name = name;
}

function SubType(){  
  SuperType.call(this, "Nicholas");

  this.age = 29;
}

var instance = new SubType();
alert(instance.name);    //"Nicholas";
alert(instance.age);     //29
{% endhighlight %}

`SuperType.call(this, "Nicholas");`이부분에서 `SubType`의 `this`를 넘겨서 `function SuperType()`의 `this`가 `SubType`의 `this`가 된다. 따라서 `SubType`의 영역에 `name`프로퍼티와 `age`프로퍼티가 생성된다.

상속에서 이 `생성자 훔치기` 기법이 유용하게 사용된다.


### 조합 상속
{% highlight javascript %}
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
  alert(this.name);
};

function SubType(name, age){  
  // 생성자 훔치기로 name 프로퍼티를 SubType에 생성
  // 이 과정에서 SuperType의 colors도 SubType에 생성됨
  SuperType.call(this, name);

  this.age = age;
}

SubType.prototype = new SuperType();

SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);  //"red,blue,green,black"
instance1.sayName();      //"Nicholas";
instance1.sayAge();       //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors);  //"red,blue,green"
instance2.sayName();      //"Greg";
instance2.sayAge();       //27
{% endhighlight %}
`SuperType`의 `this.colors`는 배열을 가리킨다. 배열은 primitive type이 아니므로 항상 레퍼런싱한다. 생성자 훔치기 기법을 이용하지 않았다면 `instance1.colors.push("black");`코드 때문에 `instance2.colors`에도 영향을 줄 것이다.

![1]({{ site.url }}/assets/javascript_inheritance1.png)
생성자 훔치기 전  


![2]({{ site.url }}/assets/javascript_inheritance2.png)
생성자 훔치기 후


이제 부모 프로퍼티를 더 이상 자식 인스턴스에서 공유하지 않게 되었다.


### 프로토타입 상속
더글라스 클락포드가 제안한 상속 방법이다.
{% highlight javascript %}
function object(o) {
  function F(){}
  F.prototype = o;
  return new F();
}
{% endhighlight %}
`object()`함수는 임시 생성자를 만들어, 주어진 객체를 생성자의 프로토타입으로 할당한 다음임시 생성자의 인스턴스를 반환한다.


{% highlight javascript %}
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};

var anotherPerson1 = object(person);
anotherPerson1.name = "Greg";
anotherPerson1.friends.push("Rob");

var anotherPerson2 = object(person);
anotherPerson2.name = "Linda";
anotherPerson2.friends.push("Barbie");

alert(person.friends);   //"Shelby,Court,Van,Rob,Barbie"
{% endhighlight %}

눈여겨 봐야할 점은, `anotherPerson1.name`과 `anotherPerson2.name`은 각각의 프로퍼티가 생겼지만, `anotherPerson1.friends`와 `anotherPerson2.friends`는 공유한다는 점이다.
![3]({{ site.url }}/assets/javascript_inheritance3.png)
