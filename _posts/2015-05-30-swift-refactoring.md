---
title:  "Swift의 Map"
date:   2015-05-30 18:36:27
categories: swift
---


아래와 같은 사용자 정보 클래스가 있다. 
{% highlight swift %}
class Member {
    var id: Int
    var name: String

    init(id: Int, name: String) {
        self.id = id
        self.name = name
    }
}
{% endhighlight %}

사용자의 정보를 담고 있는 배열 `var members: [Member]`은 다음과 같다.
{% highlight swift %}
var members: [Member] = [
    Member(id:1, name: "kim"),
    Member(id:2, name: "park"),
    Member(id:3, name: "choi"),
    Member(id:4, name: "lee"),
    Member(id:5, name: "hong")
]
{% endhighlight %}

여기서 이름만 뽑아 배열로 만들고 싶다.  
`for-in`을 이용해서 루프를 돌며 원하는 요소만 뽑아 배열에 담으면 된다.
{% highlight swift %}
var names: [String] = []
for member in members {
    names.append(member.name)
}
{% endhighlight %}

`map`을 이용해보자. `map`은 아래와 같이 작동한다.
{% endhighlight %}
[ x1, x2, ... , xn].map(f) -> [f(x1), f(x2), ... , f(xn)]
{% endhighlight %}
`map`의 파라미터에 `Member`를 인자로 갖는 함수를 넣어준다.
{% highlight swift %}
func getName(member m: Member) -> String {
    return m.name
}
var names: [String] = members.map(getName)
{% endhighlight %}

함수 대신 `lambda`를 넣으면 더 간단해진다.
{% highlight swift %}
var names: [String] = members.map({(m: Member)->String in return m.name})
{% endhighlight %}

`lambda`의 파라미터 타입과, `return` 키워드는 생략해도 된다. 이런 경우 `in`이후의 식을 리턴하게 된다.
{% highlight swift %}
var names: [String] = members.map({(m)->String in m.name})
{% endhighlight %}

`$0`을 이용해서 파라미터의 순서로 객체에 접근가능하다.
{% highlight swift %}
var names: [String] = members.map({$0.name})
{% endhighlight %}

`map`의 괄호를 생략할 수도 있다. `trail-closure`라고 한다.
{% highlight swift %}
var names: [String] = members.map{$0.name}
{% endhighlight %}







