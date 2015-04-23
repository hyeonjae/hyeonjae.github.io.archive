---
title:  "CoffeeScript로 Hubot Script만들기"
date:   2015-04-23 20:02:41
categories: 
---

hubot script는 CoffeeScript로 되어 있다.  
CoffeeScript는 Javascript로 변환될 수 있는 언어이다.  
Javascript랑 거의 같지만, 표기법만 다른 언어라고 보면 된다.  
  
주요 차이점
{% highlight javascript %}
// Javascript
function foo(x) {
    return x + 5;
    }
foo(7)
{% endhighlight %}

{% highlight coffeescript %}
# CoffeeScript
foo = (x) ->
  x + 5
foo 7
{% endhighlight %}
[http://js2.coffee/](http://js2.coffee/) 와 같은 Javascript를 CoffeeScript로 변환해주는 사이트가 있으니 걱정할 필요 없다.

---
로또번호를 무작위로 뽑아주는 hubot script를 만들어보자. 원래 CoffeeScript로 만들어야 하지만, 위의 변환사이트를 이용하면 되므로 Javascript로 만든 뒤 변환하겠다.
{% highlight javascript %}
function getLottery () {
  var i = 0,
      count = 6,
      marks = [],
      bag = [],
      upper = 45;;
  for (i=1; i<46; i++) {
    bag.push(i);
  }
  var ball,
      index;
  for (i=0; i<count; i++) {
    index = Math.floor((Math.random() * upper));
	ball = bag[index];
    bag[index] = bag[upper-1];
    marks.push(ball);
    upper -= 1;
  }
  
  return marks.sort(function(x, y){
    return x-y;
  });
}

getLottery();
{% endhighlight %}

{% highlight coffeescript %}
# Description
#   로또 번호를 무작위로 생성합니다.
#
# Commands:
#   hubot lotto - 로또 번호를 무작위로 생성합니다.
getLottery = ->
  i = 0
  count = 6
  marks = []
  bag = []
  upper = 45
  i = 1
  while i < 46
    bag.push i
    i++
  ball = undefined
  index = undefined
  i = 0
  while i < count
    index = Math.floor(Math.random() * upper)
    ball = bag[index]
    bag[index] = bag[upper - 1]
    marks.push ball
    upper -= 1
    i++
  marks.sort (x, y) ->
    x - y
    
getLottery()
{% endhighlight %}
주석을 위의 예처럼 작성해준다. 그래야 `@mybot: help`로 물어볼때 명령어 설명을 볼 수 있다.  
이제 `lotto.coffee`파일을 hubot의 scripts폴더에 넣는다. 그리고 hubot-scripts.json 파일을 수정하여, lotto script를 추가한다.
{% highlight json %}
[
    'lotto'
];
{% endhighlight %}
이제 배포하면 끝~  
bot에게 `@mybot: lotto`라고 물어보면 된다.  
  



---  
  

이전에 만들었던 사칙연산 계산기 코드도 스크립트로 추가해보겠다.  
scripts/calc.coffee에 아래 코드 추가  

{% highlight coffeescript %}
# Description
#   +, -, *, / 계산한 결과를 출력합니다.
#
# Commands:
#   hubot calc <expression> - 사칙연산(+,-,*,/) 계산 결과를 출력합니다.

lexer = (_expr) ->
  `var tokenList`
  expr = _expr
  TOKEN = [
    {
      re: /^(?:\d*\.)?\d+/
      tag: 'NUMBER'
    }
    {
      re: /^\+/
      tag: 'ADD'
    }
    {
      re: /^\-/
      tag: 'SUB'
    }
    {
      re: /^[\*]/
      tag: 'MUL'
    }
    {
      re: /^[\/]/
      tag: 'DIV'
    }
    {
      re: /^[\(]/
      tag: 'LP'
    }
    {
      re: /^[\)]/
      tag: 'RP'
    }
    {
      re: /^[ ]+/
      tag: 'WHITE'
    }
    {
      re: /^$/
      tag: 'EOS'
    }
  ]
  tokenList = []
  i = undefined
  length = TOKEN.length
  matcher = undefined
  literal = undefined
  while expr.length
    i = 0
    while i < length
      matcher = TOKEN[i].re.exec(expr)
      if matcher
        literal = expr.slice(0, matcher[0].length)
        expr = expr.substr(matcher[0].length)
        if TOKEN[i].tag == 'WHITE'
          # pass
        else if TOKEN[i].tag == 'NUMBER'
          tokenList.push
            tag: TOKEN[i].tag
            value: parseFloat(literal)
        else
          tokenList.push
            tag: TOKEN[i].tag
            value: literal
      i++
  tokenList

expr = (tokenList) ->
  value = term(tokenList)
  loop
    if tokenList[0].tag == 'ADD'
      tokenList.shift()
      value += term(tokenList)
    else if tokenList[0].tag == 'SUB'
      tokenList.shift()
      value -= term(tokenList)
    else
      break
  value

term = (tokenList) ->
  value = factor(tokenList)
  if tokenList[0].tag == 'MUL'
    tokenList.shift()
    value *= factor(tokenList)
  else if tokenList[0].tag == 'DIV'
    tokenList.shift()
    value /= factor(tokenList)
  else
  value

factor = (tokenList) ->
  if tokenList[0].tag == 'NUMBER'
    return tokenList.shift().value
  else if tokenList[0].tag == 'LP'
    tokenList.shift()
    value = expr(tokenList)
    if tokenList[0].tag == 'RP'
      tokenList.shift()
    return value
  return

module.exports = (robot) ->
  robot.hear /calc ([0-9.\(\)\+\-\*\/ ]+)/i, (msg) ->
    tokenList = lexer(msg.match[1]);
    result = expr(tokenList);
    msg.send ""+result
{% endhighlight %}

hubot-scripts.json 파일 수정
{% highlight json %}
[
  "lotto",
  "calc"
]
{% endhighlight %}
  
배포
{% highlight sh %}
$ git add scripts/calc.coffee hubot-scripts.json
$ git commit -m "calc 스크립트 추가"
$ git push heroku master
{% endhighlight %}

