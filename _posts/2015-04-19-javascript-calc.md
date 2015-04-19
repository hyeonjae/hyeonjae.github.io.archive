---
title:  "javascript로 만든 사칙연산 계산기"
date:   2015-04-19 11:45:39
categories: javascript
---

javascript로 스트링 사칙연산 계산기를 만들어 보았다.


{% highlight javascript %}
function lexer(_expr) {
  var expr = _expr;

  var TOKEN = [
    {re:/^(?:\d*\.)?\d+/, tag:'NUMBER'},
    {re:/^\+/, tag:'ADD'},
    {re:/^\-/, tag:'SUB'},
    {re:/^[\*]/, tag:'MUL'},
    {re:/^[\/]/, tag:'DIV'},
    {re:/^[\(]/, tag:'LP'},
    {re:/^[\)]/, tag:'RP'},
    {re:/^[ ]+/, tag:'WHITE'},
    {re:/^$/, tag:'EOS'}
  ];
  var tokenList = [];
  var i, length=TOKEN.length, matcher, literal;
  while (expr.length) {
    for (i=0; i<length; i++) {
      matcher = TOKEN[i].re.exec(expr);
      if (matcher) {
        literal = expr.slice(0, matcher[0].length);
        expr = expr.substr(matcher[0].length);
        if (TOKEN[i].tag === 'WHITE'){
          // pass
        } else if (TOKEN[i].tag === 'NUMBER') {
          tokenList.push({
            tag: TOKEN[i].tag, 
            value:parseFloat(literal)
          });
        } else {
          tokenList.push({
            tag:TOKEN[i].tag, 
            value:literal
          });
        }
      }
    }
  }
  return tokenList;
}

function expr(tokenList) {
  var value = term(tokenList);
  while (1) {
    if (tokenList[0].tag === 'ADD') {
      tokenList.shift();
      value += term(tokenList);
    } else if (tokenList[0].tag === 'SUB') {
      tokenList.shift();
      value -= term(tokenList);
    } else {
      break;
    }
  }
  return value;
}
function term(tokenList) {
  var value = factor(tokenList);
  if (tokenList[0].tag === 'MUL') {
    tokenList.shift();
    value *= factor(tokenList);
  } else if (tokenList[0].tag === 'DIV') {
    tokenList.shift();
    value /= factor(tokenList);
  } else {
    
  }
  return value;
}
function factor(tokenList) {
  if (tokenList[0].tag === 'NUMBER') {
    return tokenList.shift().value;
  } else if (tokenList[0].tag === 'LP') {
    tokenList.shift();
    var value = expr(tokenList);
    if (tokenList[0].tag === 'RP') {
      tokenList.shift();
    }
    return value;
  }
}
var input = "7.2 * (2 + (4-1) * 4 )+ 5";
var tokenList = lexer(input);
var result = expr(tokenList);
console.log(result);
{% endhighlight %}