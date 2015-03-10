---
title:  "javascript 스터디 #5"
date:   2015-03-10 12:26:20
categories: javascript
---



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
