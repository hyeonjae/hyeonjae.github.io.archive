---
title:  "Swift 1.2에서 Singleton 만들기"
date:   2015-05-31 10:31:37
categories: swift, singleton
---


싱글톤을 구현하는 방법은 여러가지가 있지만, `swift 1.2`에서 가장 간단하고 괜찮은 구현을 소개한다.

{% highlight swift %}
class TheSingleton {
    static let sharedInstance = TheSingleton()
    
    private init() {
        // do something
    }
}
{% endhighlight %}
우선 생성자를 `private`로 만들어, 외부에서 생성자를 호출할 수 없게 만든다.
그리고 `static` 변수가 초기화 될 때 내부에서 객체를 생성하도록 한다.
이제 이 객체를 가리키고 있는 `static` 변수에 접근하면 된다.

사용법은 다음과 같다.

{% highlight swift %}
class Member {
    static let sharedInstance = Member()
    var id: Int
    var name: String

    private init() {
        self.id = 0
        self.name = ""
    }
}
{% endhighlight %}
오직 한 개의 객체만 만들 수 있다. 

{% highlight swift %}
// m1 객체에 (1, "Park")를 할당한다.
let m1 = Member.sharedInstance
(m1.id, m1.name) = (1, "Park")

// m2 객체에 (2, "Kim")을 할당한다.
let m2 = Member.sharedInstance
(m2.id, m2.name) = (2, "Kim")

// m1, m2 모두 같은 객체를 가리키고 있다.
println("\(m1.id), \(m2.name)")
println("\(m2.id), \(m2.name)")
{% endhighlight %}

출력 결과
{% highlight sh %}
"2, Kim"
"2, Kim"
{% endhighlight %}