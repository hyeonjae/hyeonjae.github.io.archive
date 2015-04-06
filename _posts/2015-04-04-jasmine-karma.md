---
title:  "javascript 테스트 도구 jasmine과 karma"
date:   2015-04-04 19:12:39
categories: javascript
---

### Node.js 설치

[https://nodejs.org/](https://nodejs.org/) 에서 node.js 설치 프로그램 다운로드 받아 실행
{% highlight sh %}
$ node --version
v0.12.0
{% endhighlight %}


### Express 설치
{% highlight sh %}
$ mkdir myapp
$ cd myapp
$ npm init
$ npm install express --save
$ npm install express-generator -g
$ express myapp
$ cd myapp
$ npm install
$ DEBUG=myapp ./bin/www
{% endhighlight %}
중간중간 안되면 (untar error 등) sudo로 실행해보자.


### jasmine 설치 (Karma 사용시 이 과정 생략)

[https://github.com/jasmine/jasmine-npm](https://github.com/jasmine/jasmine-npm)
{% highlight sh %}
$ npm install -g jasmine
$ jasmine init
$ jasmine
{% endhighlight %}
  


---
  



### Karma 설치
[https://karma-runner.github.io/0.12/intro/installation.html](https://karma-runner.github.io/0.12/intro/installation.html)
karma설치시 `-g`옵션을 주면 글로벌 설치가능
{% highlight sh %}
$ npm install karma --save-dev
$ npm install karma-jasmine karma-chrome-launcher --save-dev
$ ./node_modules/karma/bin/karma start
{% endhighlight %}

conf파일 만들기
[https://karma-runner.github.io/0.12/intro/configuration.html](https://karma-runner.github.io/0.12/intro/configuration.html)
{% highlight sh %}
$ karma init my.conf.js
karma start my.conf.js --log-level debug --single-run
{% endhighlight %}  
  
  
### jasmine 테스트 코드 작성  

{% highlight sh %}
$ mkdir spec
$ vi myappSpec.js
{% endhighlight %}
  

{% highlight javascript %}
// myappSpec.js
describe("hello world", function() {
  it("should be succeed", function() {
    expect(false).toBe(false);
  });
  it("should be failed", function() {
    expect(true).toBe(true);
  })
});
{% endhighlight %}
  
실행
{% highlight sh %}
$ karma start my.conf.js
{% endhighlight %}

![1]({{ site.url }}/assets/karma-result.png)
  
  
기본적인 테스트
{% highlight javascript %}
// 값 비교
expect(a).toBe(b);
expect(a).not.toBe(null);

// 동등 비교
expect(a).toEqual(12);
{% endhighlight %}


Matchers 사용
{% highlight javascript %}
// 문자열 매칭
var message = "foo bar baz";

expect(message).toMatch(/bar/);
expect(message).toMatch("bar");
expect(message).not.toMatch(/quux/);
{% endhighlight %}
  
{% highlight javascript %}
// 프로퍼티 테스트
var a = {
  foo: "foo"
};

expect(a.foo).toBeDefined();
expect(a.bar).not.toBeDefined();

expect(a.foo).not.toBeUndefined();
expect(a.bar).toBeUndefined();
{% endhighlight %}
  
{% highlight javascript %}
// 참 거짓 테스트
var a, foo = "foo";

expect(foo).toBeTruthy();
expect(a).not.toBeTruthy();

expect(a).toBeFalsy();
expect(foo).not.toBeFalsy();
{% endhighlight %}
  
{% highlight javascript %}
// 멤버십 테스트
var a = ["foo", "bar", "baz"];

expect(a).toContain("bar");
expect(a).not.toContain("quux");
{% endhighlight %}
  
{% highlight javascript %}
// 값 비교
var pi = 3.1415926,
    e = 2.78;

expect(pi).toBeGreaterThan(e);
expect(e).not.toBeGreaterThan(pi);

expect(pi).not.toBeCloseTo(e, 2);
expect(pi).toBeCloseTo(e, 0);
{% endhighlight %}
  
{% highlight javascript %}
// 예외 발생 여부
var foo = function() {
  return 1 + 2;
};
var bar = function() {
  return x + 1;
};

expect(foo).not.toThrow();
expect(bar).toThrow();
{% endhighlight %}
  
{% highlight javascript %}
// 예외 종류 테스트
var foo = function() {
  throw new TypeError("foo bar baz");
};

expect(foo).toThrowError("foo bar baz");
expect(foo).toThrowError(/bar/);
expect(foo).toThrowError(TypeError);
expect(foo).toThrowError(TypeError, "foo bar baz");
{% endhighlight %}
  
  
---
  

`beforeAll`, `beforeEach`, `afterEach`, `afterAll` 사용

 - `beforeAll`: describe 당 한번만 실행, 가장 먼저 실행 됨
 - `afterAll`: describe 당 한번만 실행, 가장 나중에 실행 됨
 - `beforeEach`: 각 it 당 한번씩 실행, it이 실행 되기 전에 실행 됨
 - `afterEach`: 각 it 당 한번씩 실행, it이 실행 된 후 실행 됨
{% highlight javascript %}
describe("A spec using beforeEach and afterEach", function() {
  var foo = 0;

  beforeEach(function() {
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  beforeAll(function() {
    foo += 10;
  });

  afterAll(function() {
    foo = -1;
  });

  it("is just a function, so it can contain any code", function() {
    expect(foo).toEqual(1);
  });

  it("can have more than one expectation", function() {
    expect(foo).toEqual(1);
    expect(true).toEqual(true);
  });
});
{% endhighlight %}
  

this 사용  

{% highlight javascript %}
// this는 beforeEach - it - afterEach 간에 공유한다.
describe("A spec", function() {
  beforeEach(function() {
    this.foo = 0;
  });

  it("can use the `this` to share state", function() {
    expect(this.foo).toEqual(0);
    this.bar = "test pollution?";
  });

  it("prevents test pollution by having an empty `this` created for the next spec", function() {
    expect(this.foo).toEqual(0);
    expect(this.bar).toBe(undefined);
  });
});
{% endhighlight %}


spy는 다음 기회에 ...