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

![1]({{ site.url }}/assets/karma-results.png)

