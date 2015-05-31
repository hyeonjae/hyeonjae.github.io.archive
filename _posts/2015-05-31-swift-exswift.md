---
title:  "Swift의 확장 ExSwift"
date:   2015-05-31 11:50:55
categories: swift
---


Swift를 사용하다 보면, 다른 언어에는 있는데 Swift에는 없는 기본적인 메소드가 없어 불편한 경우가 많다. Swift에선 기본 타입에 기능을 확장할 수 있다. 기본 타입에 유용한 메소드들을 추가한 오픈소스를 소개한다.

[ExSwift](https://github.com/pNre/ExSwift)

Javascript의 [underscore](http://underscorejs.org/) 쯤으로 생각하면 된다.

`Array`, `Int`, `String`, `Dictionary` 등 주요 타입에 유용한 기능이 추가 되었다. 

이중 몇가지만 소개한다.

# Array

`each` 구문이 있다. 원소만 얻어 루프를 돌 수도 있고, 원소와 인덱스를 함께 얻어 루프를 돌 수도 있다.

{% highlight swift %}
let primes = [2, 3, 5, 7, 11]
primes.each{ e in println(e) }
primes.each{ e, i in println(e, i) }
{% endhighlight %}
출력 결과
{% highlight sh %}
2, 3, 5, 7, 11
(2, 0), (3, 1), (5, 2), (7, 3), (11, 4)
{% endhighlight %}

`first`와 `last`를 이용하여, 원소 하나만 얻어올 수 있다. 만약 원소도 없는 빈 배열이면, `nil`을 반환한다.

{% highlight swift %}
let primes = [2, 3, 5, 7, 11]
println(primes.first())
println(primes.last())
{% endhighlight %}
출력 결과
{% highlight sh %}
2
11
{% endhighlight %}

그런데, `Swift 1.2`에 `first`와 `last`가 기본 프로퍼티로 추가되었다.
따라서 `ExSwift`의 `.first()`와 `.last()`를 사용할 필요가 없다.

{% highlight swift %}
class Member: Printable {
    var id: Int
    var name: String
    
    init(id: Int, name: String) {
        self.id = id
        self.name = name
    }
    
    internal var description: String {
        get {
            return "Id: \(self.id), Name: \(self.name)"
        }
    }
}

let a = [
    Member(id: 1, name: "Park"),
    Member(id: 2, name: "Kim")
]

println("first: \(a.first)")
println("last: \(a.last)")
{% endhighlight %}

`contains`  
배열에 원하는 값이 있는지 테스트한다.
{% highlight swift %}
let array = ["A", "B", "C", "D"]
array.contains("A", "C")
// → true

array.contains("Z")
// → false
{% endhighlight %}

`any`, `all`  
any조건, all조건을 만족하는지 테스트한다.
{% highlight swift %}
let array = [4, 6, 8, 10]

/* Positive */
array.all { $0.isEven() }
// true

/* Negative */
array.all { $0 < 8 }
// false
{% endhighlight %}

`zip`  
두 배열을 순서끼리 묶어준다.
{% highlight swift %}
[1, 2].zip(["A", "B"])
// [[1, "A"], [2, "B"]]
{% endhighlight %}
`implode`  
다른 언어에서 `join()`에 해당한다.
{% highlight swift %}
let array = ["Hello", "World"]
array.implode(", ")
// "Hello, World"
{% endhighlight %}
`flatten`  
중첩 배열을 선형으로 만들어준다.
{% highlight swift %}
let array = [1, 2, [3, 4, [5]], 6]
array.flatten() as NSNumber[]
// [1, 2, 3, 4, 5, 6]
{% endhighlight %}

# Int
`clamp`  
범위에 속하는 값을 반환한다. 범위보다 작으면 하한값을, 범위보다 크면 상한값을 반환한다.
{% highlight swift %}
1.clamp(2...4)
// 2

3.clamp(2...4)
// 3

5.clamp(2...4)
// 4
{% endhighlight %}


# String
`explode`  
다른 언어의 `split()`에 해당하는 메소드이다. 특정 문자를 기준으로 문자열을 자른다. 
{% highlight swift %}
"Hello World".explode(" ")
// ["Hello", "World"]
{% endhighlight %}

`matches`  
정규식 패턴에 일치하는 문자열을 추출한다.
{% highlight swift %}
let str = "AB[31]"
let matches = str.matches("\\d+")!
let range = matches[0].rangeAtIndex(0)

let start = range.location
let end = start + range.length

str[start...ed]
// 31
{% endhighlight %}

# Dictionary
Dictionary타입 역시 iterative한 타입이기 때문에, `each`나 `any`, `all`같은 배열에서 사용한 메소드를 이용할 수 있다.  
`map`  
Dictionary의 모든 원소에 순차적으로 접근하면서, `key`와 `value`를 넘겨 받아 원하는 `key`-`value` 쌍을 반환하여 Dictionary를 만들 수 있다.
{% highlight swift %}
let dictionary = [ "A": 1, "B": 2, "C": 3 ]
let mapped = dictionary.map{ ($0.lowercaseString, $1 + 1) }
println(mapped) 
// [a: 2, b: 3, c: 4]
{% endhighlight %}

`mapValues`  
`map`와 비슷하지만, 반환 할 `key`는 원래의 `key`와 같다.
{% highlight swift %}
let dictionary = [ "A": 1, "B": 2, "C": 3 ]
let mapped = dictionary.mapValues{ $1 + 1 }
println(mapped) 
// [A: 2, B: 3, C: 4]
{% endhighlight %}
