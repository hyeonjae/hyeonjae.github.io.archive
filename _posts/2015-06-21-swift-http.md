---
title:  "SwiftHttp JSON Parameter"
date:   2015-06-21 12:29:40
categories: swift
---


# 개요
Swift로 iOS앱을 만들던 중 HTTP Request를 사용하기 편한 라이브러리가 필요하게 되었다.  
SwiftHTTP, Alamofire 두 오픈소스가 가장 많이 사용되는 것 같다.  
별 이유 없이 SwiftHTTP를 선택하였다.  
iOS는 GUI에서 작동하기 때문에, 대부분의 HTTP Request 라이브러리가 비동기 요청만 지원한다.  
기존에 만들던 동기 요청부분을 없애고 SwiftHTTP로 모두 교체하였다.  
  
# 문제
HTTP method 중 PUT, POST에 JSON파라미터를 입력받아 HTTP Request하는 API가 JSON파라미터로 Dictionary타입만 입력 받도록 구현되어 있었다.  
다른 라이브러리도 마찬가지였다.  
배열도 JSON형태중 하나인데, SwiftHTTP는 Object형태만 입력받도록 되어 있는 문제가 있었다.  
// json object type
{% highlight json %}
{
    "id": 1002,
    "name": "swift"
    "cc": [1,2,5,6]
}
{% endhighlight %}

// json array type
{% highlight json %}
[
    {"id": 1002, "name": "swift"},
    {"id": 1003, "name": "objective-c"}
]
{% endhighlight %}
  
  
# 방법
해당 요청만 SwiftHTTP의 API를 사용하지 않고 직접 Request할지, SwiftHTTP소스를 수정할지 고민하다가 일단 SwiftHTTP Github에 issue를 올렸다.  
SwiftHTTP 제작자가 친절하게 답변해주었다. 배열도 원래 JSON형태중 하나이기 때문에 Dictionary만 입력받는 것이 아닌 Object를 입력받도록 하는 것이 맞긴 하다는 답변이었다.  
그러면서 Dictionary로 배열을 넘기는 Trick을 알려주면서, Dictionary가 아닌 Object를 입력 받도록 코드 수정하여 Pull request해보라고 하였다.  
입력 파라미터 타입을 모두 Dictionary에서 Object로 바꾸었다.  
{% highlight swift %}
// before
public func createRequest(url: NSURL, method: HTTPMethod, parameters: Dictionary<String,AnyObject>?) -> (request: NSURLRequest, error: NSError?) {}

// after
public func createRequest(url: NSURL, method: HTTPMethod, parameters: AnyObject?) -> (request: NSURLRequest, error: NSError?) {}
{% endhighlight %}
어짜피 JSONSerializer는 Object타입을 입력 받기 때문에 큰 문제가 없다.  
일부 코드에서 Dictionary인지 Array인지 구별해야할 필요가 있어, Type detect하여 분기문으로 처리하도록 하는 코드를 추가하였다.

# 적용
이제 commit하고, push해야한다.

하지만 난 이 프로젝트의 contirbutor가 아니기 때문에 직접 올릴 수 없다.  
우선 SwiftHTTP프로젝트를 fork하여 내 계정으로 가져와야 한다.  
그리고 수정한 코드를 fork한 SwiftHTTP에 push한다.  
그리고 여기서 pull request를 작성한다. target은 원래의 SwiftHTTP로 하면 된다.  
  
pull request comment에 fixed #20을 추가하면, 이전에 올린 이슈에 자동으로 링크가 걸린다.  
  
이전에 올린 이슈에서 이 이슈를 해결한 pull request를 볼 수 있고  
그 pull request를 accept하여 merge할 경우 이슈도 자동으로 closed된다.  