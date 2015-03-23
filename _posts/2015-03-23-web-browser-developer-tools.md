---
title:  "브라우저 별 디버깅 도구"
date:   2015-03-23 09:58:20
categories: browser
---



# 브라우저 점유율 [1][1] [2][2] [3][3] [4][4]
조사 기관, 조사 방법에 따라 차이가 있다.
크게 PC 브라우저와 모바일 브라우저로 나눴다. 특히 PC 브라우저의 국내와 세계의 점유율 트랜드를 살펴보겠다.

국내는 IE가 68%의 점유율로 1위, Chrome이 28%로 2위이다.  
통계를 내준 사이트를 보면 IE는 80%에서 빠른 속도로 줄어들고 있다.  
국내는 IE와 Chrome의 점유율이 96%를 차지하고 있다.

세계적으로는 IE가 1위라는 통계도 있지만, Chrome가 53%로 1위라는 의견이 우세하다.  
같은 통계를 기준으로 하면 IE가 20%로 2위, Firefox가 19%로 3위를 차지한다.  

결론은 IE, Chrome가 가장 많고, Firefox도 상당하다. 조사 방법, 조사 기관에 따라 IE가 1위로 보는 곳도 있으니 국내만 IE 쓴다는 식의 편견은 위험하다. IE도 버전별로 기능이 상이하기 때문에 IE 전체의 점유율은 무의미하다.

# 브라우저 디버깅 도구
그럼 점유율 높은 브라우저의 개발자 도구(디버깅 도구)를 살펴보겠다.
Chrome, IE, Firefox, Safari 위주로 보겠다.


## Chrome
![1]({{ site.url }}/assets/chrome-developer-tools.jpg)
크롬에선 개발자 도구를 통해 Javascript 디버깅이 가능하다.
`F12`(`option+command+i`)를 누르면 개발자 도구가 나온다.
`Elements`에선 html코드와 css코드를 볼 수 있고, 수정도 할 수 있다.
`Sources`에선 html파일과 js와 css, 이미지파일과 같은 리소스를 볼 수 있다.
여기서 사용된 Javascript를 열람하고, 수정, 실행할 수 있다.
Break point를 설정하고, 디버그 모드로 돌리면, step 단위로 실행해 볼 수 있다.
`Console`은 console 로그로 출력 결과를 볼 수 있고, 간단하게 js를 실행할 수 있다.

## IE
IE도 비슷하다. IE 버전별로 혹은, 모바일버전으로 시뮬레이션 할 수 도 있다.
[https://www.modern.ie/ko-kr](https://www.modern.ie/ko-kr)에서 IE6~IE11까지 테스트해 볼 수 있다.
IE는 점유율이 높고, 특히 버전별로 이슈가 있는 만큼 IE에서 테스트는 중요하다.

## Firefox
![2]({{ site.url }}/assets/firefox-developer-tools.png)
국내에는 점유율이 거의 없지만, 세계적으로는 IE만큼 사용하는 브라우저이다.
[https://developer.mozilla.org/ko/docs/Tools/Debugger](https://developer.mozilla.org/ko/docs/Tools/Debugger)에서 다양한 개발툴을 찾아볼 수 있다.



참고자료  
 > - [1](http://marketshare.hitslink.com/browser-market-share.aspx?qprid=3&qpcustomb=0)
 > - [2](http://netmarketshare.com/)
 > - [3](http://prolite.tistory.com/304)
 > - [4](http://gs.statcounter.com/#desktop-browser-ww-monthly-201402-201503)
