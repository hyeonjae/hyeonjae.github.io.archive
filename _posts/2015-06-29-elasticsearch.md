---
title:  "Elasticsearch"
date:   2015-06-29 09:14:26
categories: elasticsearch
---


# 설치

Elasticsearch [다운로드](https://www.elastic.co/downloads)

{% highlight sh %}
$ tar xvfz elasticsearch-1.6.0.tar.gz
$ cd elasticsearch-1.6.0
$ bin/elasticsearch
{% endhighlight %}

# 데이터 구조

| RDBMS  | ElasticSearch  |
|---|---|
| Database  | Index  |
| Table  | Type  |
| Row  | Document  |
| Column  | Field  |
| Scheme  | Mapping  |
    
# Method  

| HTTP  | CRUD  |
|---|-----|
| Post | Create  |
| Get | Read  |
| Put | Update  |
| Delete | Delete  |


# Rest API
{% highlight sh %}
$ curl -XPUT http://localhost:9200/{index}/{type}/{document id}/ -d '{data}'
{% endhighlight %}



# Elasticsearch 실행
`start.sh`를 만든다.
{% highlight sh %}
#!/bin/bash

export ES_HEAP_SIZE=256m
export ES_HEAP_NEWSIZE=128m
export JAVA_OPT="-server -XX:+AggressiveOpts -XX:UseCompressedOops -XX:MaxDirectMemorySize -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly"

ES=$HOME/workspace/elasticsearch/elasticsearch-1.6.0
$ES/bin/elasticsearch -Des.pidfile=$ES/bin/es.pid -Des.config=$ES_NODE/config/elasticsearch.yml -Djava.net.preferIPv4Stack=true -Des.max-open-files=true > /dev/null 2>&1 &

ps -ef | grep elasticsearch
{% endhighlight %}
실행 권한을 주고, 실행
{% highlight sh %}
$ chmod +x start.sh
$ ./start.sh
{% endhighlight %}


# PUT을 이용하여 데이터 추가
{% highlight sh %}
$ curl -XPUT http://localhost:9200/books/book/1 -d '
{
  "title": "Elasticsearch Guide",
  "author": "Kim",
  "date": "2015-06-25",
  "pages": 250
}'
{% endhighlight %}
다음과 같은 출력이 나오면 입력 성공
{% highlight sh %}
{"_index": "books","_type":"book","_id":"1","_version":1,"created":true}
{% endhighlight %}
입력 된 순간에만 `"created":true`처럼 `"created"`값이 `true`이다.

# GET을 이용하여 데이터 조회
{% highlight sh %}
$ curl -XGET http://localhost:9200/books/book/1
{% endhighlight %}
{% highlight sh %}
{"_index":"books","_type":"book","_id":"1","_version":1,"found":true,
  "_source":  {
    "title":"Elasticsearch Guide",
    "author":"Kim",
    "date":"2015-06-25",
    "pages":250,
  }
}
{% endhighlight %}
메타정보와 함께 `"_source"`아래에 조금 전 입력한 데이터가 들어있다.


# POST를 이용하여 데이터 입력
POST를 이용하여 데이터를 입력하는 경우에는 document id를 생략하고 입력할 수 있다. 이 경우 임의의 id가 부여된다.
{% highlight sh %}
$ curl -XPOST http://localhost:9200/books/book/ -d '
{
  "title": "Elasticsearch Guide",
  "author": "Kim",
  "date": "2015-06-25",
  "pages": 250
}'
{% endhighlight %}
입력 결과
{% highlight sh %}
{"_index":"books","_type":"book","_id":"AU4eDMKk5_2R8Icukymj","_version":1,"created":true}
{% endhighlight %}

# PUT를 이용하여 데이터 덮어쓰기
`"author"`의 값을 배열로 바꾸고, `"pages"`의 값도 300으로 변경해보겠다.
{% highlight sh %}
$ curl -XPOST http://localhost:9200/books/book/ -d '
{
  "title": "Elasticsearch Guide",
  "author": ["Kim", "Lee"],
  "date": "2015-06-25",
  "pages": 300
}'
{% endhighlight %}
`"_version"`은 2로, `"created"`는 false가 된다.
{% highlight sh %}
{"_index":"books","_type":"book","_id":"AU4eDMKk5_2R8Icukymj","_version":2,"created":false}
{% endhighlight %}

다시 GET을 이용하여 출력해보면 (`?pretty=true`로 출력 결과를 보기 좋게 할 수 있다)
{% highlight sh %}
$ curl -XGET http://localhost:9200/books/book/AU4eDMKk5_2R8Icukymj?pretty=true
{% endhighlight %}
아래와 같이 변경 된 것을 볼 수 있다.
{% highlight sh %}
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "AU4eDMKk5_2R8Icukymj",
  "_version" : 2,
  "found" : true,
  "_source": {
    "title": "Elasticsearch Guide",
    "author": ["Kim", "Lee"],
    "date": "2015-06-25",
    "pages": 300
  }
}
{% endhighlight %}

# `_source`만 출력하기
`/_source`로 `"_source"`부분만 가져오자.
{% highlight sh %}
$ curl -XGET http://localhost:9200/books/book/AU4eDMKk5_2R8Icukymj/_source?pretty=true
{% endhighlight %}
{% highlight sh %}
{
  "title": "Elasticsearch Guide",
  "author": ["Kim", "Lee"],
  "date": "2015-06-25",
  "pages": 300
}
{% endhighlight %}

# DELETE로 데이터 삭제하기
{% highlight sh %}
$ curl -XDELETE http://localhost:9200/books/book/AU4eDMKk5_2R8Icukymj
{% endhighlight %}
삭제후 GET으로 조회하면 `"found"`값이 false가 된다.
{% highlight sh %}
{"_index":"books","_type":"book","_id":"AU4eDMKk5_2R8Icukymj","found":false}
{% endhighlight %}
id로 삭제해도 메타정보는 남이 있기 때문에, 이후 다시 입력해도 `"version"`값은 계속 유지되어 있다.

# POST로 데이터 업데이트하기
`"category"`필드를 추가하고 값을 `"ICT"`로 입력해보자.
필드를 추가하기 위해선 `/_update` API와 `"doc"`매개변수를 이용한다.
{% highlight sh %}
$ curl -XPOST http://loc00/books/book/AU4eDMKk5_2R8Icukymj/_update -d '
{
  "doc": {
    "category": "ICT"
  }
}'
{% endhighlight %}
{% highlight sh %}
{"_index":"books","_type":"book","_id":"AU4eDMKk5_2R8Icukymj","_version":3}
{% endhighlight %}

잘 들어 갔는지 확인해보자
{% highlight sh %}
$ curl -XGET http://localhost:9200/books/book/AU4eDMKk5_2R8Icukymj/_source?pretty=true
{% endhighlight %}
{% highlight sh %}
{
  "title":"Elasticsearch Guide",
  "author":["Kim","Lee"],
  "date":"2015-06-25",
  "pages":300,
  "category":"ICT"
}
{% endhighlight %}

# script 이용한 데이터 업데이트
생략...


# 파일을 이용한 데이터 처리
`-d` 옵션으로 입력할 데이트를 literal하게 입력하지 않고 파일로 입력 할 수 있다.  
`book_1`파일
{% highlight json %}
{
  "title": "Elasticsearch Guide",
  "author": ["Kim", "Lee"],
  "date": "2015-06-25",
  "pages": 300
}
{% endhighlight %}

파일 이름이 `book_1`이라면 `@book_1`
{% highlight sh %}
$ curl -XPOST http://localhost:9200/books/book/ -d @book_1
{% endhighlight %}


-----



# bulk API

buik API는 한번의 API 호출로 여러개의 명령을 매우 빠르게 처리할 수 있다.
REST API에서 `/_bulk`로 끝나면 다음과 같은 JSON형태의 입력이 와야 한다.
{% highlight text %}
action_and_meta_data\n
optional_source\n
action_and_meta_data\n
optional_source\n
...
action_and_meta_data\n
optional_source\n
{% endhighlight %}

# Action종류
 - index: create와 비슷하지만 이미 있는 데이터면 덮어쓴다.
 - create: 이미 있는 데이터면 오류 발생
 - delete: 삭제
 - update: 수정

# bulk 형태

`bulk_1`이라는 파일을 만들고 아래와 같이 입력한다.
{% highlight json %}
{ "index": { "_index": "books", "_type": "book", "_id": "1" } }
{ "title": "Elasticsearch Guide", "author": "Kim", "pages": 250 }
{ "index": { "_index": "books", "_type": "book", "_id": "2" } }
{ "title": "Elasticsearch Easy Guide", "author": "Lee", "pages": 300 }
{% endhighlight %}

# bulk file 
위에서 만든 bulk파일을 입력해보자.
{% highlight sh %}
$ curl -XPOST http://localhost:9200/_bulk?pretty --data-binary @bulk_1
{% endhighlight %}

# bulk UDP 
UDP로 원격에서 bulk를 이용할 수도 있다.

UDP를 이용하기 위해선 Elasticsearch 설정 파일을 수정해야한다.
`config/elasticsearch.yml`파일에 다음 내용을 추가한다.
{% highlight text %}
bulk.udp.enabled: true
{% endhighlight %}
그리고 Elasticsearch를 다시 실행시킨다.
모든 데이터를 지우고 UDP로 데이터를 입력해보자.
{% highlight sh %}
$ curl -XDELETE http://localhost:9200/books
$ cat bulk_1 | nc -w 0 -u localhost 9700
{% endhighlight %}
이제 확인해보자.
{% highlight sh %}
$ curl -XGET http://localhost:9200/books/book/1/_source?pretty
$ curl -XGET http://localhost:9200/books/book/2/_source?pretty
{% endhighlight %}
출력 결과
{% highlight sh %}
{ "title": "Elasticsearch Guide", "author": "Kim", "pages": 250 }
{ "title": "Elasticsearch Easy Guide", "author": "Lee", "pages": 300 }
{% endhighlight %}



----


# 노드 바인딩

여러 서버에서 하나의 클러스터를 구성하는 방법이다.
`config/elasticsearch.yml`에 `cluster.name`만 같은 이름으로 통일 시켜주면 된다.
  
이제 elasticsearch를 재시작해보자. 각각의 서버 혹은 서로 다른 터미널에서 실행시키면 자동으로 9200, 9201, ... 9299 포트가 할당된다.

각 노드끼리는 9300, 9301로 http 통신한다.



----



# 검색

#### books/book에서 hamlet으로 검색
{% highlight sh %}
curl "http://localhost:9200/books/book/_search?q=hamlet&pretty"
{% endhighlight %}

#### books에서 검색
{% highlight sh %}
curl "http://localhost:9200/books/_search?q=hamlet&pretty"
{% endhighlight %}

#### multi-tenancy 검색도 가능
{% highlight sh %}
curl "http://localhost:9200/books,magazines/_search?q=time&pretty"
{% endhighlight %}

#### 전체 클러스터 검색
`_all` 사용하여 전체 검색
{% highlight sh %}
curl "http://localhost:9200/_all/_search?q=time&pretty"
{% endhighlight %}
생략해도 전체 검색 된다.
{% highlight sh %}
curl "http://localhost:9200/_search?q=time&pretty"
{% endhighlight %}

#### 특정 필드 검색
`_search?q=필드명:질의어`
{% highlight sh %}
curl "http://localhost:9200/_search?q=title:time&pretty"
{% endhighlight %}

#### 특정 필드 검색 default field
`_search?q=질의어&df=필드명`
{% highlight sh %}
curl "http://localhost:9200/_search?q=time&df=title&pretty"
{% endhighlight %}

#### AND, OR
`_search?qtitle:time AND machine` 사용 가능. 단 url인코딩 `%20`로 사용해야 함.
{% highlight sh %}
# curl "http://localhost:9200/_search?q=title:time AND machine&pretty"
curl "http://localhost:9200/_search?q=title:time%20AND%20machine&pretty"
{% endhighlight %}

#### default_operator

`default_operator`를 지정하면 질의어 사이의 공백을 모두 조건 명령어(AND, OR)로 검색한다.
{% highlight sh %}
curl "http://localhost:9200/_search?q=title:time%20machine&default_operator=AND&pretty"
{% endhighlight %}



----


# Host에 Vagrant설치

# Vagrant용 Guest OS 다운로드 및 설치
{% highlight sh %}
$ vagrant box add centos64minimal https://github.com/tommy-muehle/puppet-vagrant-boxes/releases/download/1.0.0/centos-6.6-x86_64.box 
$ vagrant init centos65minimal
$ vagrant up
{% endhighlight %}
http://www.vagrantbox.es/

# Vagrant에 jdk, elasticsearch 설치
jdk rpm파일과 elasticsearch파일을 다운로드 받는다.
vagrant의 `/vagrant`는 호스트 OS의 현재경로로 마운트되어있다. Vagrantfile이 있는 곳에  jdk와 ealsticsearch파일을 옮겨 놓으면, 게스트 OS에서 `/vargrant`에서 접근 가능하다.
{% highlight sh %}
$ vagrant ssh
$ rpm -Uvh jdk-xxx.xxx.rpm
$ tar xvfz elasticsearch-1.x.x.tar.gz
{% endhighlight %}

# Vagrantfile의 포트 설정
게스트OS의 elasticsearch는 9200~9300포트를 Listen한다.
호스트OS의 적당한 포트에 9200으로 연결하도록 설정하자.
`Vagrantfile`을 수정한다.

{% highlight text %}
config.vm.network "forwarded_port", guest: 9200, host: 9900
{% endhighlight %}

이제 호스트OS에서 `localhost:9900`으로 접속하면 게스트OS의 `:9200`으로 연결된다.

# Vagrant의 방화벽 끄기
{% highlight sh %}
$ sudo service iptables stop
{% endhighlight %}


---