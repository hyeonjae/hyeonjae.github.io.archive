---
title:  "Hubot, Slack 그리고 Heroku"
date:   2015-04-21 19:51:27
categories: hubot
---


# hubot 준비
{% highlight sh %}
$ npm install -g yo generator-hubot
$ mkdir mybot
$ cd mybot
$ yo hubot
{% endhighlight %}
{% highlight text %}
Owner: {{YOUR_ID}}
Bot name: mybot
Description: A simple my bot
Bot adapter: slack
{% endhighlight %}

# hubot 테스트
{% highlight sh %}
$ bin/hubot --name mybot
{% endhighlight %}
{% highlight text %}
mybot ping
mybot> PONG
{% endhighlight %}
`mybot ping`라고 입력시 mybot이 PONG라고 답하면 Okay


# slack에서 토큰 발급 받기
[slack] - [Configure Integrations] - [Hubot add]  
Username은 mybot으로 지어주고 [Add Hubot Integration]누르면  
API Token값이 발급된다.
`xoxb-4*******63-SxZ8o**********7DYRjWBx6`  
[Save Integration]누르면 끝

# hubot - slack 테스트
터미널에서 아래와 같이 hubot실행
{% highlight sh %}
$ HUBOT_SLACK_TOKEN=xoxb-452*****33-OyZi***************DWup7 ./bin/hubot --adapter slack
{% endhighlight %}
slack에서 생성한 hubot에게 ping을 날려, PONG라고 답이 오는지 확인한다.
{% highlight text %}
@myapp: ping
{% endhighlight %}

# heroku
[heroku](https://www.heroku.com/) 에서 회원가입하고, heroku toolbelt를 설치한다.  
그리고 app을 생성하자.
{% highlight sh %}
$ cd mybot
$ heroku login
$ heroku create # 이때 생성된 주소 기억하기
                # http://**********.herokuapp.com
{% endhighlight %}

# heroku 테스트
{% highlight sh %}
$ heroku open
{% endhighlight %}
웹 사이트 접속 되면 성공

# heroku에 hubot 배포 및 slack 연동
먼저 Procfile확인
{% highlight sh %}
$ cat Procfile
web: bin/hubot --adapter slack
{% endhighlight %}
위와 다르게 출력된다면 수정해주자.
{% highlight sh %}
$ echo "web: bin/hubot --adapter slack" > Procfile
{% endhighlight %}

heroku 설정
{% highlight sh %}
$ heroku config:add HUBOT_URL=http://**********.herokuapp.com
$ heroku config:add HUBOT_SLACK_TOKEN=xoxb-452*****33-OyZi***************DWup7
$ git add .
$ git commit -m "Hubot"
$ git push heroku master
$ heroku ps:scale web=1
{% endhighlight %}

# 테스트
slack에 가서 mybot에게 ping을 날려본다.
{% highlight text %}
@mybot: ping
{% endhighlight %}
PONG이라고 응답이 오면 성공!!  
  
이제 필요한 hubot script를 작성해서 배포하면 된다.