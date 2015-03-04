---
title:  "javascript 스터디 #2"
date:   2015-03-04 15:00:00
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

자바스크립트에는 5가지 primitive 타입과 객체 타입이 있다.

 - undefined
 - boolean
 - string
 - number
 - function
 - object

그리고 주의해야할 값으로 `null`과 `undefined`가 있다.

`null`은 초기화 된 객체의 값이고, `undefined`는 아직 초기화 되지 않은 값이다. 쉽게 생각하면, 아직 선언도 안한 변수에 접근할 때 `undefined`이다. 따라서 어떤 객체를 `undefined`로 초기화 하는 일이 없도록 하자.

---


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

### number
  
IEEE-754 형식을 사용하고, 8진법이나 16진법 리터럴 표현도 가능하다.
{% highlight javascript %}
var octNum = 012;
var hexNum = 0xFF;
{% endhighlight %}

부동소숫점도 마찬가지다.
{% highlight javascript %}
var floatNum1 = 3.14;
var floatNum2 = 0.14;
var floatNum3 = .14;  // 비추천
{% endhighlight %}

한가지 특이한 점은 소숫점 뒤의 숫자가 없거나 0이면 정수로 간주한다는 것이다.
{% highlight javascript %}
var floatNum1 = 1.;     // 1로 간주
var floatNum2 = 10.0;   // 10으로 간주
{% endhighlight %}

C의 `sscanf()`, Java의 `parseInt()`에 해당하는 것이 자바스크립트에는 세 가지가 있다.
 - `Number()`
 - `parseInt()`
 - `parseFloat()`

`Number()`의 경우 조금 복잡하다.
 - boolean은 0, 1로 변환한다.
 - null은 0을 반환한다.
 - undefined는 NaN을 반환한다.
 - 빈 문자열은 0을 반환한다.
 - 이외 숫자 형식에 맞지 않는 문자열은 NaN을 반환한다.

`parseInt()`는 조금 더 간편하다.
  
적당히 알아서 정수를 뽑아준다. 두 번째 매개변수가 진법을 나타내므로 8이나 16을 넘겨 해당 진법에 맞게 숫자로 변환 할 수 있다.


---


### string
  
PHP와는 달리, 큰따옴표와 작은따옴표가 완전히 같은 것으로 인식한다.

문자열로 변환하는 방법은 세 가지가 있다. 
 - `num.toString()`
 - `String(num)`
 - `"" + num`

num이 숫자 형식인 경우 `num.toString()`의 매개변수로 진법을 나타내는 숫자를 입력하여 변환할 수 있다.
{% highlight javascript %}
var num = 13;
num.toString();     // "13"
num.toString(2);    // "1101"
num.toString(5);    // "23"
num.toString(10);   // "13"
num.toString(16);   // "d"
{% endhighlight %}

num이 `null`이나 `undefined`일 수도 있다. 이런 경우를 대비하여 `String()`를 쓰자. `String()`는 다음과 같은 규칙으로 문자로 변환한다.
 - `toString()` 메서드가 존재하면 `.toString()`를 호출하여 그 결과를 반환
 - `null`이면 `"null"`을 반환
 - `undefined`이면 `"undefined"`을 반환

---

### object

아래와 같이 객체를 생성할 수 있다.
{% highlight javascript %}
var o = new Obejct();
{% endhighlight %}
  
매개변수가 없으면 괄호를 생략해도 되지만, 추천하지 않는다.
{% highlight javascript %}
var o = new Obejct;   // 비추천
{% endhighlight %}
  
자바스크립트의 모든 객체는 Obejct 타입의 프로퍼티와 메서드를 상속받는다.
 - `constructor`
 - `hasOwnProperty()`
 - `isPrototypeOf()`
 - `propertyIsEnumerable()`
 - `toLocaleString()`
 - `toString()`
 - `valueOf()`

---

## 증감 연산자

정수인 경우는 문제 없지만, 문자열이나 부동소수점인 경우, 해당 객체의 `valueOf()` 메서드로 값을 얻고 그 값에 증감 연산자를 적용한다.


## 비트 연산자

ECMAScript에서 모든 숫자는 IEEE-754의 64비트 형식으로 되어 있지만, 비트 연산자는 32비트 숫자로 변환 한 뒤 연산하고, 다시 64비트로 변환한다.
  
## 시프트 연산
  
`>>`와 `>>>`만 알아보겠다.

`>>`는 부호 비트는 유지하고 나머지 값을 오른쪽으로 시프트 한다.
`>>>`는 부호 비트 상관없이 전체를 오른쪽으로 시프트 한다. 따라서 이 경우 부호가 바뀔 수 있다.

---

## Quiz

다음 코드의 실행 결과는 ?

{% highlight javascript %}
var num = 5;
(function() {
  alert( num );

  var num = 7;
})();
{% endhighlight %}

5가 나올 것 같지만, 결과는 `undefined`


