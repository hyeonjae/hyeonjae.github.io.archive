---
title:  "Java의 ArrayList"
date:   2015-03-09 09:38:32
categories: java
---


{% highlight java %}
private Object[] elementData;
private int size;

public ArrayList(int initialCapacity) {
    super();
    this.elementData = new Object[initialCapacity];
}
{% endhighlight %}
기본적으로 Object 배열이다. 배열의 크기는 `size`에 담는다.


{% highlight java %}
public boolean add(E e) {
    ensureCapacity(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
{% endhighlight %}
`add()`할 경우, 배열 맨 마지막에 값을 넣는다. 넣기 전에 `ensureCapacity()`를 호출하는데, 배열의 크기가 넉넉한지 체크한다.


{% highlight java %}
public void ensureCapacity(int minCapacity) {
    modCount++;
    int oldCapacity = elementData.length;
    if (minCapacity > oldCapacity) {
        Object oldData[] = elementData;
        int newCapacity = (oldCapacity * 3)/2 + 1;
        if (newCapacity < minCapacity)
            newCapacity = minCapacity;
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
}
{% endhighlight %}
`ensureCapacity()`는 기존 배열의 크기와 데이터를 넣게 될 경우의 크기를 비교한다.
만약 기존 배열의 크기가 더 작으면, 약 1.5배 늘린 배열을 만들고, 이전 값을 모두 복사한다.

jdk1.7에는 1.5배 늘리는 부분이 `grow()`라는 메소드로 분리되었다.
{% highlight java %}
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
{% endhighlight %}
기존 `(oldCapacity * 3)/2 + 1;`는 3을 곱하는 과정에서 integer overflow 발생할 수 있다.
jdk1.7에는 3을 곱한뒤 2로 나누는 방식이 아닌 `oldCapacity + (oldCapacity >> 1);`처럼 2로 나눈 값을 더하는 방식으로 1.5배의 크기를 구했다.



{% highlight java %}
rangeCheck(index);
rangeCheckForAdd(index);
{% endhighlight %}
boundary 체크한다.


### 총평
생각보다 간단하다. 복잡한 알고리즘을 이용하지 않는다.
