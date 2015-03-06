---
title:  "Java에서 Counter 자료구조 만들기"
date:   2015-03-06 15:29:10
categories: java
---

## Counter란

파이썬에 [Counter](https://docs.python.org/2/library/collections.html#collections.Counter)라는 기본 자료구조가 있다. 일종의 multiset으로, key의 갯수를 value로 갖는 map 형태의 자료구조이다.
  
자바에는 없어서 찾아보았다.
  
먼저 아래와 같은 사전 생성기를 만들었다. 같은 String이 무작위로 담겨있는 List를 만든다.
{% highlight java %}
private List<String> makeDictionary() {
	String[] fruitList = {"apple", "banana", "cherry", "grape",
		"kiwi", "lemon", "mango", "orange"};
	int[] distribution = { 2, 3, 9, 19, 21, 17, 4, 1};
	List<String> fruits = new ArrayList<String>(Arrays.asList(fruitList));
	
	for(int i=0; i < fruitList.length; i++) {
		for(int n=0; n < distribution[i]; n++) {
			fruits.add(fruitList[i]);
		}
	}
	Collections.shuffle(fruits, new Random(System.nanoTime()));
	
	return fruits;
}
{% endhighlight %}
  
  
---
  
  
## 방법 1.
{% highlight java %}
// naive, without java 8, no libraries
public void counter1(List<String> fruits){
	Map<String, Integer> counter = new HashMap<String, Integer>();
	
	for (String fruit : fruits) {
		Integer previousValue = counter.get(fruit);
		
		counter.put(fruit, previousValue == null ? 1 : previousValue+1 );
	}
}
{% endhighlight %}
나이브한 방법이다. HashMap에 기존에 key가 없으면 1을 넣고, 있으면 1을 더한다.
  
  
---
  
  
## 방법 2.
{% highlight java %}
// with java 8
public void counter2(List<String> fruits){
	Map<String, Integer> counter = new HashMap<String, Integer>();
	
	for (String fruit : fruits) {
		counter.merge(fruit, 1,  (oldValue, one) -> oldValue+one);
	}
}
{% endhighlight %}
  
  
---
  
  
## 방법 3.
{% highlight java %}
// with java 8
public void counter3(List<String> fruits){
	Map<String, Integer> counter = new HashMap<String, Integer>();
	
	for (String fruit : fruits) {
		counter.compute(fruit, (key, oldValue) -> oldValue == null ? 1 : oldValue+1 );
	}		
}
{% endhighlight %}
`compute()`를 이용한 방법이다. key가 없는 경우를 위한 `computeIfAbsent()`가 조금 더 나은 방법이다. 방법 5에서 다루겠다.
  
  
---
  
  
## 방법 4.
{% highlight java %}
// with java 7, using AtomicInteger
public void counter4(List<String> fruits){
	Map<String, AtomicInteger> counter = new HashMap<String, AtomicInteger>();
	
	for (String fruit : fruits) {
		if(!counter.containsKey(fruit)) {
			counter.put(fruit,  new AtomicInteger(1));
		} else {
			counter.get(fruit).incrementAndGet();
		}
	}			
}
{% endhighlight %}
java 7에서 `AtomicInteger`를 이용한 방법이다. 기본적으로 `containsKey()`를 이용한다.
  
  
---
  
  
## 방법 5.
{% highlight java %}
// with java 8, using LongAdder
public void counter5(List<String> fruits){
	Map<String, LongAdder> counter = new HashMap<String, LongAdder>();
	
	for (String fruit : fruits) {
		counter.computeIfAbsent(fruit, (t) -> new LongAdder()).increment();
	}	
}
{% endhighlight %}
`LongAdder`와 `computeIfAbsent()`를 이용하여 깔끔하게 구현하였다.
  
  
---
  
  
## 방법 6.
{% highlight java %}
// using int[]
public void counter6(List<String> fruits){
	Map<String, int[]> counter = new HashMap<String, int[]>();
	
	for (String fruit : fruits) {
		int[] previousValue = counter.get(fruit);
		if( previousValue == null ) {
			counter.put(fruit, new int[] { 1 } );
		} else {
			previousValue[0]++;
		}
	}		
}
{% endhighlight %}
`int`는 `null`을 대입할 수 없고, `Integer`는 매번 객체가 생성된다. 이 두 방법의 단점을 극복한 방법이 `int[]`를 이용한 것이다. 속도도 빠르다.
  
  
---
  
  
## 방법 7.
{% highlight java %}
// with java 8, using stream
public void counter7(List<String> fruits){
	Stream<String> stream = fruits.parallelStream();
	Map<String, Long> counter = stream.collect(Collectors.groupingBy(String::toString, Collectors.counting()));
}
{% endhighlight %}
java 8의 `Stream`을 이용한 우아한 방법이다.
  
  
---
  
  
## 방법 8.
{% highlight java %}
// with java 8, using stream
public void counter8(List<String> fruits){
	Stream<String> stream = fruits.parallelStream();
	Map<String, Integer> counter = stream.collect(Collectors.toConcurrentMap(k->k, v->1, Integer::sum));
}
{% endhighlight %}
역시 java 8의 `Stream`을 이용하였다.

  
  
---
  
  
참고자료

> - [http://codereview.stackexchange.com/questions/57078/more-elegant-way-to-increment-integers-in-map](http://codereview.stackexchange.com/questions/57078/more-elegant-way-to-increment-integers-in-map)  
> - [http://www.programcreek.com/2013/10/efficient-counter-in-java/](http://www.programcreek.com/2013/10/efficient-counter-in-java/)  
> - [http://stackoverflow.com/questions/25448145/frequency-of-words-in-list-of-strings-of-strings](http://stackoverflow.com/questions/25448145/frequency-of-words-in-list-of-strings-of-strings)  
> - [http://stackoverflow.com/questions/21978653/java-8-jdk8-streams-functions-map-reduce-to-group-liststring-into-mapstring](http://stackoverflow.com/questions/21978653/java-8-jdk8-streams-functions-map-reduce-to-group-liststring-into-mapstring)  
> - [http://www.leveluplunch.com/java/examples/find-frequency-of-object-within-collection/](http://www.leveluplunch.com/java/examples/find-frequency-of-object-within-collection/)  
> 
