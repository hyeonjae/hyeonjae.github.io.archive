---
title:  "javascript 스터디 #2"
date:   2015-03-04 12:00:00
categories: javascript
---

## 스트릭트 모드

스크립트 맨 위에 `"use strict";` 가 있을 경우 자바스크립트 엔진은 이후의 코드를 스트릭트 모드에서 동작시킨다. 스트릭트 모드를 함수에만 적용하고 싶은 경우 아래와 같이 함수 시작 부분에 `"use strict";`를 넣어주면 된다.

{% highlight javascript %}
function doSomething(){
    "use strict";
    // do something
}
{% endhighlight %}

---

## 스코프
 - ECMAScript에서는 실행 컨텍스트(Excution Context, EC)라고 부른다.
 - 오로지 function scope만 존재
 - file scope는 존재 하지 않기 때분에 여러 js 파일간 변수명 충돌 가능하다.
 - 즉시실행함수 기법으로 file scope 흉내 가능
 

{% highlight javascript %}
(function() {
  // do something
})();
{% endhighlight %}
js 파일 전체를 `function() { }` function scope로 감싸고, js파일을 읽자마자 실행하도록 `(function() {})();`한다.

다른 언어들의 block scope가 자바스크립트에는 없기 때문에, `{ }`블록 이후에도 변수가 사라지지 않는다. 만약 현재 스코프에 해당 변수가 없으면 상위 스코프(실제로는 실행 컨텍스트)에서 찾아서 사용한다. 이를 `스코프 체인(Scope chain)`이라고 한다. 

---


## 타입
자바스크립트에는 5가지 타입이 있다.

 - undefined
 - boolean
 - string
 - number
 - object
 - function

그리고 주의해야할 값으로 `null`과 `undefined`가 있다.

`null`은 초기화 된 객체의 값이고, `undefined`는 아직 초기화 되지 않은 값이다. 쉽게 생각하면, 아직 선언도 안한 변수에 접근할 때 `undefined`이다. 따라서 어떤 객체를 `undefined`로 초기화 하는 일이 없도록 하자.

## boolean
자바스크립트의 boolean 타입은 다른 언어에서 정의한 것과 다르다.

| 테이터 타입 | true로 변환되는 값 | false로 변환되는 값 |
|---|---|---|
| boolean | true | false |
| string | 비어 있지 않은 모든 문자열 | ""(빈 문자열)  |
| number | 0이 아닌 모든 숫자(무한대 포함) | 0, NaN |
| object | 모든 객체 | null |
| undefined | 해당 없음 | undefined |




---



다음 코드의 실행 결과는 ?
{% highlight javascript %}
var num = 5;
(function() {
  alert( num );

  var num = 7;
})();
{% endhighlight %}

5가 나올 것 같지만, 결과는 `undefined`


