---
title:  "javascript 스타일"
date:   2015-03-04 11:00:00
categories: javascript
---

#툴
JSLint나 JSHint를 사용해라. 참 좋다.

#들여쓰기
웬만하면 공백 4자로 해라.
텝은 편집기마다 크기가 다르기 때문에 안 좋다.
공백 2는 너무 작고, 공백 8은 너무 길다.
더글라스 클락포드 스타일인 공백 4로 고고

#문장종료
javascript는 문장 끝에 `;`를 안써도 알아서 잘 넣어주는데, 그래도 헷갈리는 경우가 있으니 꼭 명시해주자

{% highlight javascript %}
function foo() {
    return
    {
        title: "javascript",
        author: "Nicholas"
    }
}
{% endhighlight %}
위에처럼 

{% highlight javascript %}
function foo() {
    return;
    {
        title: "javascript",
        author: "Nicholas"
    }
}
{% endhighlight %}

