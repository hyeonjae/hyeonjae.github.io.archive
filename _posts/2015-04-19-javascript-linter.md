
---
title:  "JSLint, JSHint, ESLint"
date:   2015-04-19 12:06:40
categories: javascript
---

#Lint란
원래 컴퓨터 프로그래밍에서 에러가 발생할 만한 코드를 미리 알려 주는 것을 말한다. 역사는 Unix와 C언어가 만들어지던 1970년대에 YACC를 만든 사람이 처음으로 C언어용 Lint를 만든 것에서 시작했다.[1]  
실행 하지 않고 잠재적인 오류를 찾아 알려주는 것을 정적 분석이라 한다.  
  
Javascript는 스크립트 언어라 실행 중이 아닌 때에는 정확한 분석이 어렵다. 그래서 이런 도구들이 생겨났다.  
  
# JSLint
더글라스 크락포드가 만든 Javascript 정적 분석 도구이다. 너무 엄격해서 이와 비슷한 JSHint를 많이 쓴다고 한다.[2]

# JSHint
JSLint에서 파생되어 안톤 코발료프가 관리하고 있는 툴이다. 대부분의 옵션을 선택적으로 켜고 끌 수 있다. 

# ESLint
자카스가 개발하고 관리하고 있는 툴이다. 

---

# 옵션
JSHint 기분 몇가지 옵션을 정리해보았다.


# node

 * Node.js를 사용하고 있음을 JSHint에 알려줌
 * 전역 스코프 사용 금지 옵션이 `true`여도 `console.log` 같은 것도 금지 하는 것은 말이 안 되기 때문

# browser

 * document, navigator 같은 브라우저 전용 전역 변수 사용 허용

# esnext

 * ECMAScript 6 코드를 사용하고 있음

# bitwise

 * bitwise연산자(^, |, &) 사용금지 
 * javascript에선 bit단위 연산 할 일이 별로 없고, or 연산자(&&)와 헷갈릴 소지가 있음

# camelcase

 * 이 옵션은 deprecated되었고, 다음 JSHint 버전에선 없어질 예정
 * 코딩 스타일 룰을 넣고 싶으면 JSCS project 참고

# curly

 * 반복문이나 조건문에서 반드시 curly braces를 넣어야 함

# eqeqeq

 * ==, != 대신 ===, !== 사용하기

# immed

{% highlight javascript %}
// bad
function () {
}();

// good
(function () {
})();
{% endhighlight %}
 * deprecated된 옵션, 코딩 스타일은 JSCS project 참고

# indent

 * tab 사이즈 통일 (보통 2나 4)
 * deprecated된 옵션, 코딩 스타일은 JSCS project 참고

# latedef

 * 정의하기 전에 변수 사용 금지 (Hoisting 경고)

# newcap

 * 생성자 함수는 대문자로 시작, new로 생성해야함을 나타냄
 * deprecated된 옵션, 코딩 스타일은 JSCS project 참고

# noarg

 * `arguments.caller` 와 `arguments.callee` 사용 금지
 * 이 두 프로퍼티는 최적화를 어렵게 한다.

# quotmark

 * 인용부호의 통일 (single 혹은 double)
 * deprecated된 옵션, 코딩 스타일은 JSCS project 참고

# undef
 
 * 선언하지 않고 사용하는 변수(전역변수) 사용 금지
 * 아래와 같은 경우 방지해준다.
{% highlight javascript %}
function test() {
  var myVar = 'Hello, World';
  console.log(myvar); // Oops, typoed here. JSHint with undef will complain
}
{% endhighlight %}

# unused

 * 사용하지 않는 변수 알림

# strict

 * 모든 함수는 strict 모드여야 함

# validthis

 * 잘못된 this 사용 알림
 * 이 옵션은 반드시 함수 스코프 내에서만 사용해야함 [1](https://jslinterrors.com/option-validthis-cant-be-used-in-a-global-scope)

# globals

 * 전역 스코프 사용 금지

[1]: http://en.wikipedia.org/wiki/Lint_(software)
[2]: http://blog.outsider.ne.kr/1007