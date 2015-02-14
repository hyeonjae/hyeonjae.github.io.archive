---
layout: post
title:  "Java의 ConcurrentHashMap"
date:   2015-02-14 21:36:00
categories: tech
---


### HashMap, HashTable

두 자료구조 모두 hash 기반의 key-value 연관 자료형이다.

HashMap는 key값에 null을 넣을 수 있고, HashTable은 그렇지 않다는 점을 제외하곤, 기본적인 사용법은 같다.
HashTable과 HashMap는 Map인터페이스를 사용한다. 유일한 key값을 바탕으로 value값을 찾는다.

중요한 차이점은 HashTable은 동기화가 되어 있고, HashMap은 그렇지 않다는 것이다.
따라서 HashTable은 여러 thread에서 함께 사용해도 안전하지만, HashMap은 적절하게 동기화 처리 해주지 않으면 안전하지 않다. 앞으로 소개할 ConcurrentHashMap은 동기화 처리가 되어 있는 HashTable을 개선한 자료구조로, Java5부터 소개 되었다. 

### ConcurrentHashMap

이름에서 알 수 있듯 ConcurrentHashMap은 HashMap이나 HashTable처럼 key-value 형태의 자료구조다. Concurrent뒤에 HashMap이라는 이름을 사용하고 있지만, ConcurrentHashMap은 null을 허용하지 않는다는 점이나 동기화가 되어 있어 thread-safe 하다는 점에서 HashTable과 더 비슷하다.
HashTable는 내부적으로 모든 쓰기 작업에 lock을 걸어 동기화를 구현하였지만, ConcurrentHashMap은 여러 부분으로 구역을 나누어 처리하여, 좀 더 많은 쓰레드가 동시에 접근할 수 있다. 



### 구현

ConcurrentHashMap 구현의 주요 목표는 다음과 같다.
1. 값의 변경을 최소화 하면서 동시 읽기 작업(get, iterator) 구현
2. HashMap의 메모리 사용 수준을 유지, 또는 개선


ConcurrentHashMap은 ConcurrentMap 인터페이스와 AbstractMap 추상 클래스를 상속받았다. 

{% highlight java %}
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable {
        // ...
}
{% endhighlight %}




### JDK별 발전과정

JDK 버전별로 ConcurrentHashMap을 살펴보겠다.

http://grepcode.com/project/repository.grepcode.com/java/root/jdk/openjdk/
위 링크에 나온 ConcurrentHashMap은 

6-b14, 6-b27, 7-b147, 7u40-b43, 8-b132가 있다.

| 버전     | 라인수  |
|----------|---------|
| 6-b14    |  1318   |
| 6-b27    |  1318   |
| 7-b147   |  1522   |
| 7u40-b43 |  1619   |
| 8-b132   |  6312   |

JDK1.7에서 JDK1.8로 가면서 코드 라인수가 4배나 늘었다. 라인수만 봐도 JDK1.8에서 큰 변화가 있다는 것을 알 수 있다. 우선 JDK1.6에서 JDK1.7의 소소한 변화와, JDK1.8로의 큰 변화를 분석해보겠다.

diff툴로 각 버전별로 비교를 해보았다.
6-b14, 6-b27은 주석에서 sun이 oracle로 바뀐 것 이외에는 차이점이 없었다.

이제부터 6-14, 6-b27은 JDK1.6이라고 부르고, 7-b147, 7u40-b43은 JDK1.7, 8-b132는 JDK1.8이라고 부르겠다.


JDK1.6과 JDK1.7을 비교보았다.

각자 `HashEntry<K,V>` inner 클래스가 있다.

JDK1.6의 HashEntry
{% highlight java %}
static final class HashEntry<K,V> {
    final K key;
    final int hash;
    volatile V value;
    final HashEntry<K,V> next;

    HashEntry(K key, int hash, HashEntry<K,V> next, V value) {
        this.key = key;
        this.hash = hash;
        this.next = next;
        this.value = value;
    }

    @SuppressWarnings("unchecked")
    static final <K,V> HashEntry<K,V>[] newArray(int i) {
        return new HashEntry[i];
    }
}
{% endhighlight %}

JDK1.7의 HashEntry
{% highlight java %}
static final class HashEntry<K,V> {
    final int hash;
    final K key;
    volatile V value;
    volatile HashEntry<K,V> next;

    HashEntry(int hash, K key, V value, HashEntry<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    /**
     * Sets next field with volatile write semantics.  (See above
     * about use of putOrderedObject.)
     */
    final void setNext(HashEntry<K,V> n) {
        UNSAFE.putOrderedObject(this, nextOffset, n);
    }

    // Unsafe mechanics
    static final sun.misc.Unsafe UNSAFE;
    static final long nextOffset;
    static {
        try {
            UNSAFE = sun.misc.Unsafe.getUnsafe();
            Class k = HashEntry.class;
            nextOffset = UNSAFE.objectFieldOffset
                (k.getDeclaredField("next"));
        } catch (Exception e) {
            throw new Error(e);
        }
    }
}
{% endhighlight %}

`HashEntry<K,V>`는 key-value로 이루어진 클래스다. ConcurrentHashMap의 가장 작은 단위이다.

`final HashEntry<K,V> next`가 `volatile HashEntry<K,V> next`로 바뀌었다.
final는 변하지 않는 값이지만, volatile는 변할 수 있는 값이다. volatile를 쓰면, 쓰지 않았을 때와 비교해서, 항상 최신 값을 보장 받을 수 있다. 여러 쓰레드가 값을 변경하여도, 바로바로 변경사항이 반영된다. 당연한 말처럼 들릴 수 있지만, 사실 보통 변수(volatile가 없는)는 어느 정도 캐싱되어 최신값을 보장받을 수 없다.[1][2]
final, volatile를 적절히 잘 사용하여, 별도의 lock없이도 다른 thread의 값 변경을 잘 반영할 수 있다.[3]


key값으로 value를 구하는 `get` 함수이다.
{% highlight java %}
public V get(Object key) {
    int hash = hash(key.hashCode());
    return segmentFor(hash).get(key, hash);
}
{% endhighlight %}

ConcurrentHashMap의 내부는 Segment(Reentrant Lock를 상속받은 클래스)들로 나누어져 있다. 
{% highlight java %}
static final class Segment<K,V> extends ReentrantLock implements Serializable {
    // ...
    transient volatile HashEntry<K,V>[] table;
    // ...
}
{% endhighlight %}
각 Segment에는 HashEntry배열이 table이라는 이름으로 있다.


key값에 대해 `hashCode()`의 기준으로 별도의 hash함수로 해쉬값을 구한다.
hash 알고리즘은 JDK1.8을 빼고 동일하다
{% highlight java %}
private static int hash(int h) {
    // Spread bits to regularize both segment and index locations,
    // using variant of single-word Wang/Jenkins hash.
    h += (h <<  15) ^ 0xffffcd7d;
    h ^= (h >>> 10);
    h += (h <<   3);
    h ^= (h >>>  6);
    h += (h <<   2) + (h << 14);
    return h ^ (h >>> 16);
}
{% endhighlight %}


각 segment는 이 해쉬값으로 찾는다. 해쉬값으로 segment를 찾는 함수는 아래와 같다.

JDK1.6
{% highlight java %}
final Segment<K,V> segmentFor(int hash) {
    return segments[(hash >>> segmentShift) & segmentMask];
}
{% endhighlight %}

JDK1.7
{% highlight java %}
@SuppressWarnings("unchecked")
private Segment<K,V> segmentForHash(int h) {
    long u = (((h >>> segmentShift) & segmentMask) << SSHIFT) + SBASE;
    return (Segment<K,V>) UNSAFE.getObjectVolatile(segments, u);
}

@SuppressWarnings("unchecked")
static final <K,V> Segment<K,V> segmentAt(Segment<K,V>[] ss, int j) {
    long u = (j << SSHIFT) + SBASE;
    return ss == null ? null :
        (Segment<K,V>) UNSAFE.getObjectVolatile(ss, u);
}
{% endhighlight %}



여기서 segmentShift와 segmentMask, ssifht, ssize 등 주요 값에 대해 찾아보았다.

#### MAX_SEGMENTS

segment의 최대 허용 개수이다. 
기본으로 1<<16 (2^15)로 세팅되어 있다. 
{% highlight java %}
static final int MAX_SEGMENTS = 1 << 16;
{% endhighlight %}

#### concurrencyLevel

ConcurrentHashMap의 생성자 3번째 인자로, MAX_SEGMENTS보다 크면 MAX_SEGMENTS값으로 세팅된다.
{% highlight java %}
if (concurrencyLevel > MAX_SEGMENTS)
    concurrencyLevel = MAX_SEGMENTS;
{% endhighlight %}

ConcurrentHashMap 생성자에서 3번째 인자를 생략할 경우 기본값 DEFAULT_CONCURRENCY_LEVEL이 적용되고 이 값은 16으로 세팅되어 있다.
{% highlight java %}
static final int DEFAULT_CONCURRENCY_LEVEL = 16;
{% endhighlight %}

#### ssize, sshift
concurrencyLevel보다 같거나 큰, 가장 작은 2의 지수값과 그의 계수 값이 된다.
concurrencyLevel이 16이면 sshift는 4, ssize는 16이 된다.
concurrencyLevel이 16보다 크고 32보다 작은 값이면 (예 17) sshift는 5, ssize는 32가 된다.
{% highlight java %}
int sshift = 0;
int ssize = 1;
while (ssize < concurrencyLevel) {
    ++sshift;
    ssize <<= 1;
}
{% endhighlight %}



#### segmentShift, segmentMask
sshift가 4, ssize가 16인 경우 
segmentShift는 28이고, segmentMask는 15가 된다. (바이너리로 0b00000000_00000000_00000000_00001111)
{% highlight java %}
segmentShift = 32 - sshift;
segmentMask = ssize - 1;
{% endhighlight %}


생성자에서 ssize값만큼의 segment를 생성한다.

JDK1.6
{% highlight java %}
@SuppressWarnings("unchecked")
static final <K,V> Segment<K,V>[] newArray(int i) {
    return new Segment[i];
}

this.segments = Segment.newArray(ssize);
{% endhighlight %}

JDK1.7 [4]
{% highlight java %}
Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];

this.segments = ss;
{% endhighlight %}


concurrencyLevel크기의 Segment 클래스 배열에 Segment객체를 생성하여 넣는다.
JDK1.6
{% highlight java %}
if (initialCapacity > MAXIMUM_CAPACITY)
    initialCapacity = MAXIMUM_CAPACITY;
int c = initialCapacity / ssize;
if (c * ssize < initialCapacity)
    ++c;
int cap = 1;
while (cap < c)
    cap <<= 1;

for (int i = 0; i < this.segments.length; ++i)
    this.segments[i] = new Segment<K,V>(cap, loadFactor);
{% endhighlight %}

JDK1.7
{% highlight java %}
if (initialCapacity > MAXIMUM_CAPACITY)
    initialCapacity = MAXIMUM_CAPACITY;
int c = initialCapacity / ssize;
if (c * ssize < initialCapacity)
    ++c;
int cap = MIN_SEGMENT_TABLE_CAPACITY;
while (cap < c)
    cap <<= 1;
// create segments and segments[0]
Segment<K,V> s0 =
    new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                     (HashEntry<K,V>[])new HashEntry[cap]);
Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
this.segments = ss;
{% endhighlight %}


Segment 클래스의 생성자를 살펴보자

JDK1.6
{% highlight java %}
void setTable(HashEntry<K,V>[] newTable) {
    threshold = (int)(newTable.length * loadFactor);
    table = newTable;
}

Segment(int initialCapacity, float lf) {
    loadFactor = lf;
    setTable(HashEntry.<K,V>newArray(initialCapacity));
}
{% endhighlight %}

JDK1.7
{% highlight java %}
Segment(float lf, int threshold, HashEntry<K,V>[] tab) {
    this.loadFactor = lf;
    this.threshold = threshold;
    this.table = tab;
}
{% endhighlight %}

Segment 클래스의 생성자는 JDK1.6과 JDK1.7이 표현 방식만 다를 뿐 내용은 같다.
loadFactor는 default값으로 0.75가 세팅되어 있다.
{% highlight java %}
static final float DEFAULT_LOAD_FACTOR = 0.75f;
{% endhighlight %}
threshold는 테이블의 크기가 threshold를 넘을 경우 rehash 하는 기준값이다. capacity의 75%수준이 되면 rehash된다.

실제로 ConcurrentHashMap에 값을 넣는 put 함수를 살펴보면 threshold값을 넘을 때 rehash 함수를 호출하는 것을 볼 수 있다.

JDK1.6
{% highlight java %}
V put(K key, int hash, V value, boolean onlyIfAbsent) {
    lock();
    try {
        int c = count;
        if (c++ > threshold)        // count값이 threshold를 넘으면
            rehash();               // rehash 한다.
        HashEntry<K,V>[] tab = table;
        int index = hash & (tab.length - 1);
        HashEntry<K,V> first = tab[index];
        HashEntry<K,V> e = first;
        while (e != null && (e.hash != hash || !key.equals(e.key)))
            e = e.next;

        V oldValue;
        if (e != null) {
            oldValue = e.value;
            if (!onlyIfAbsent)
                e.value = value;
        }
        else {
            oldValue = null;
            ++modCount;
            tab[index] = new HashEntry<K,V>(key, hash, first, value);
            count = c;
        }
        return oldValue;
    } finally {
        unlock();
    }
}
{% endhighlight %}

JDK1.7
{% highlight java %}
final V put(K key, int hash, V value, boolean onlyIfAbsent) {
    HashEntry<K,V> node = tryLock() ? null :
        scanAndLockForPut(key, hash, value);
    V oldValue;
    try {
        HashEntry<K,V>[] tab = table;
        int index = (tab.length - 1) & hash;
        HashEntry<K,V> first = entryAt(tab, index);
        for (HashEntry<K,V> e = first;;) {
            if (e != null) {
                K k;
                if ((k = e.key) == key ||
                    (e.hash == hash && key.equals(k))) {
                    oldValue = e.value;
                    if (!onlyIfAbsent) {
                        e.value = value;
                        ++modCount;
                    }
                    break;
                }
                e = e.next;
            }
            else {
                if (node != null)
                    node.setNext(first);
                else
                    node = new HashEntry<K,V>(hash, key, value, first);
                int c = count + 1;

                // count값이 threshold를 넘고, table의 크기가 
                // MAXIMUM_CAPACITY를 넘지 않으면,
                // rehash 한다.
                if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                    rehash(node);
                else
                    setEntryAt(tab, index, node);
                ++modCount;
                count = c;
                oldValue = null;
                break;
            }
        }
    } finally {
        unlock();
    }
    return oldValue;
}
{% endhighlight %}


이제 생성자를 살펴보겠다.

JDK1.6
{% highlight java %}
public ConcurrentHashMap(int initialCapacity, 
                        float loadFactor, int concurrencyLevel)
{% endhighlight %}
initialCapacity는 지정하지 않으면 `DEFAULT_INITIAL_CAPACITY = 16`이 세팅된다. 
loadFactor도 `DEFAULT_LOAD_FACTOR = 0.75f` 기본 세팅된다.
concurrencyLevel도 역시 `DEFAULT_CONCURRENCY_LEVEL = 16`이다.


{% highlight java %}
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
{% endhighlight %}
생성자가 호출되면, 가장먼저 하는 일이, 입력 파라미터의 유효성 검사이다. 0보다 작으면 모두 예외를 발생 시킨다.


{% highlight java %}
    if (concurrencyLevel > MAX_SEGMENTS)
        concurrencyLevel = MAX_SEGMENTS;
{% endhighlight %}
concurrencyLevel가 너무 큰 값이 입력되면, `MAX_SEGMENTS = 1 << 16` 값으로 변경해준다.

{% highlight java %}
    // Find power-of-two sizes best matching arguments
    int sshift = 0;
    int ssize = 1;
    while (ssize < concurrencyLevel) {
        ++sshift;
        ssize <<= 1;
    }
    segmentShift = 32 - sshift;
    segmentMask = ssize - 1;
    this.segments = Segment.newArray(ssize);
{% endhighlight %}
concurrencyLevel보다 같거나 큰, 가장 작은 2의 거듭제곱만큼의 크기로 Segment 배열을 만든다. Segment 배열의 각 원소마다 Table(HashEnty의 배열)이 들어간다.
HashTable을 Segment로 구간별로 나누어서, 서로 다른 Segment끼리는 thread-safe한 점을 이용한다.


{% highlight java %}
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
{% endhighlight %}
initialCapacity도 최대값을 넘으면 최대값 `MAXIMUM_CAPACITY = 1 << 30` 으로 세팅한다.

{% highlight java %}
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity)
        ++c;
    int cap = 1;
    while (cap < c)
        cap <<= 1;

    for (int i = 0; i < this.segments.length; ++i)
        this.segments[i] = new Segment<K,V>(cap, loadFactor);
{% endhighlight %}
약 initialCapacity만큼의 Segment배열을 만든다.


To be continue ...

[1]: http://kwanseob.blogspot.kr/2012/08/java-volatile.html
[2]: http://jeremymanson.blogspot.kr/2008/11/what-volatile-means-in-java.html
[3]: http://books.google.co.kr/books?id=O6fJBAAAQBAJ&pg=PA28&lpg=PA28&dq=volatile+map+java+iterator+concurrentmodificationexception&source=bl&ots=YhhEoYJIeG&sig=BfmN06mvGZ6EBYaT24JRsro7xV4&hl=ko&sa=X&ei=h_BjVNW1D-bOmwW904HwAQ&ved=0CE4Q6AEwBQ#v=onepage&q=volatile%20map%20java%20iterator%20concurrentmodificationexception&f=false
[4]: http://comments.gmane.org/gmane.comp.java.jsr.166-concurrency/10944