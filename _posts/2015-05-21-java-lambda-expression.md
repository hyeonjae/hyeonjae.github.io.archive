---
title:  "Java의 lambda expression"
date:   2015-05-22 20:59:14
categories: java, lambda
---

# Lambda Expressions

`Person` 클래스를 아래와 같이 만들었다.
{% highlight java %}
public class Person {

    public enum Sex {
        MALE, FEMALE
    }

    String name;
    LocalDate birthday;
    Sex gender;
    String emailAddress;

    public int getAge() {
        // ...
    }

    public void printPerson() {
        // ...
    }
}
{% endhighlight %}
이름, 성별, 생일, 이메일 주소 등이 있다.  
  
어떤 서비스에서 `List<Person>`를 사용한다고 가정하자.

### Approach 1: Create Methods That Search for Members That Match One Characteristic

 `List<Person>`중에서 특정 조건을 만족하는 `Person`을 출력해보자.
{% highlight java %}
public static void printPersonsOlderThan(List<Person> roster, int age) {
    for (Person p : roster) {
        if (p.getAge() >= age) {
            p.printPerson();
        }
    }
}
{% endhighlight %}
`List<Person>`을 for문으로 순회하면서 if문으로 검사한다. 특정 조건(`>= age`)을 만족하는 `Person`만 출력한다.

1. 순회 (`for`)
2. 원소의 특정값 접근 (`p.getAge()`)
3. 검색 조건 (`>= age`)
4. 만족할 경우의 작업 (`p.printPerson()`)

앞으로의 글은 위의 4가지 작업으로 나누어 코드를 개선하는 내용이다.
  
### Approach 2: Create More Generalized Search Methods

조금 더 일반적인 검색조건을 만들어보자.
{% highlight java %}
public static void printPersonsWithinAgeRange(
    List<Person> roster, int low, int high) {
    for (Person p : roster) {
        if (low <= p.getAge() && p.getAge() < high) {
            p.printPerson();
        }
    }
}
{% endhighlight %}
기존에는 `>= age`를 만족하는 `Person`을 찾았지만, 이제는 `low <= && < high`를 만족하는 `Person`을 찾아 출력해야 한다. 만약 age뿐만 아니라, 특정 성별, 특정 생일인 `Person`을 찾아 출력하고 싶다면, 코드 수정이 불가피하다. 검색 조건을 일반화하여 언제든지 변경 가능하게 코드를 개선하자.


### Approach 3: Specify Search Criteria Code in a Local Class

{% highlight java %}
public static void printPersons(
    List<Person> roster, CheckPerson tester) {
    for (Person p : roster) {
        if (tester.test(p)) {
            p.printPerson();
        }
    }
}
{% endhighlight %}
위의 코드는 검색 조건에 해당하는 부분을 parameter로 전달 받았다.
`CheckPerson`은 interface로 만들자.

{% highlight java %}
interface CheckPerson {
    boolean test(Person p);
}
{% endhighlight %}
그리고 실제 `CheckPerson`의 구현은 아래처럼 만든다.
{% highlight java %}
class CheckPersonEligibleForSelectiveService implements CheckPerson {
    public boolean test(Person p) {
        return p.gender == Person.Sex.MALE &&
            p.getAge() >= 18 &&
            p.getAge() <= 25;
    }
}
{% endhighlight %}
이제 제일 처음에 만든 `printPersons()`는 아래처럼 사용하면 된다.
{% highlight java %}
printPersons(roster, new CheckPersonEligibleForSelectiveService());
{% endhighlight %}


### Approach 4: Specify Search Criteria Code in an Anonymous Class

파라미터에 new로 생성한 클래스는 익명 클래스로 바꿔보자.
{% highlight java %}
printPersons(
    roster,
    new CheckPerson() {
        public boolean test(Person p) {
            return p.getGender() == Person.Sex.MALE
                && p.getAge() >= 18
                && p.getAge() <= 25;
        }
    }
);
{% endhighlight %}
`interface ChectPerson`을 매번 구현한 클래스를 만들지 않아도 되기 떄문에, 불필요한 코드가 많이 줄었다.  
`CheckPerson`은 하나의 메소드만 있다. 이런 경우 `lambda expression`을 이용해보자. 

### Approach 5: Specify Search Criteria Code with a Lambda Expression

위의 `CheckPerson` 인터페이스는 `Functional interface`라고 한다.  
하나의 추상 메소드를 갖는 경우를 `Functional interface`라고 한다.  
실제 코드는 아래와 같다.
{% highlight java %}
printPersons(
    roster,
    (Person p) -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25
);
{% endhighlight %}
코드가 훨씬 더 줄어들었다.

### Approach 6: Use Standard Functional Interfaces with Lambda Expressions

다시한번 `CheckPerson` 인터페이스를 보자.
{% highlight java %}
interface CheckPerson {
    boolean test(Person p);
}
{% endhighlight %}
하나의 파라미터에, `boolean`을 리턴하는 하나의 메소드만 있는 인터페이스이다.  
고작 이런 단순한 인터페이스를 위해 매번 새로 인터페이스를 만들 필요는 없다. `java.util.function`에 이미 이런 인터페이스가 있다.

{% highlight java %}
interface Predicate<T> {
    boolean test(T t);
}
{% endhighlight %}
제네릭 인터페이스이고 실제 사용은 아래와 같이 한다.
{% highlight java %}
interface Predicate<Person> {
    boolean test(Person t);
}
{% endhighlight %}
`Predicate<Person>`을 파라미터로 입력받도록 원래 코드도 수정하자.
{% highlight java %}
public static void printPersonsWithPredicate(
    List<Person> roster, Predicate<Person> tester) {
    for (Person p : roster) {
        if (tester.test(p)) {
            p.printPerson();
        }
    }
}
{% endhighlight %}
사용은 `lambda expression`에 타입을 생략한 채 사용하면 된다.
{% highlight java %}
printPersonsWithPredicate(
    roster,
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25
);
{% endhighlight %}


### Approach 7: Use Lambda Expressions Throughout Your Application

또 `lambda expression`을 쓸 수 있는 곳을 찾아보자.
{% highlight java %}
public static void printPersonsWithPredicate(
    List<Person> roster, Predicate<Person> tester) {
    for (Person p : roster) {
        if (tester.test(p)) {
            p.printPerson();
        }
    }
}
{% endhighlight %}
특정 조건을 만족하는 경우 `printPerson()` 대신 다른 동작을 하도록 변경해보자. 역시 `lambda expression`을 이용하면 된다.  
`lambda expression`을 사용하기 위해선, `functional interface`를 구현해야하는데, `Person`을 파라미터로 받고, `void`를 리턴하는 추상 메소드를 가지고 있으면 된다.  
`Consumer<T>` 인터페이스가 적합하다. 이 인터페이스는 `accept(T t)` 메소드를 가지고 있다.  

{% highlight java %}
public static void processPersons(
    List<Person> roster,
    Predicate<Person> tester,
    Consumer<Person> block) {
        for (Person p : roster) {
            if (tester.test(p)) {
                block.accept(p);
            }
        }
}
{% endhighlight %}

보통 `PrintPerson`이라는 인터페이스에 `accept` 추상 메서드를 만든다.
{% highlight java %}
interface PrintPerson {
    void accept(Person p);
}
{% endhighlight %}
`PrintPerson` 인터페이스를 구현한 `PrintPersonImpl` 클래스를 만든다.  그리고 `accept`에 `printPerson()`을 호출하도록 구현한다.
{% highlight java %}
class PrintPersonImpl implements PrintPerson {
    void accept(Person p) {
        p.printPerson();
    }
}
{% endhighlight %}
`processPersons`는 `PrintPersonImpl`를 파라미터로 입력받아, 특정 조건일때, 입력받은 `PrintPersonImpl`의 `accept` 메서드를 호출하게 한다.
{% highlight java %}
public static void processPersons(
    List<Person> roster,
    Predicate<Person> tester,
    PrintPersonImpl printer ) {
        for (Person p : roster) {
            if (tester.test(p)) {
                printer.accept(p);
            }
        }
}
{% endhighlight %}

이제 `processPersons`를 호출할때, `new PrintPersonImpl()`로 `PrintPersonImpl`객체를 생성하여 넘겨주면 된다. 
{% highlight java %}
processPersons(
    roster, 
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25,
    new PrintPersonImpl()
);
{% endhighlight %}

하지만 매번 `PrintPerson` 인터페이스를 구현한 클래스를 만들어서 사용하지 않고, 익명 클래스로 바로 사용할 수도 있다.
{% highlight java %}
processPersons(
    roster, 
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25,
    new PrintPerson() {
        p.printPerson();
    }
);
{% endhighlight %}

그리고 하나의 메서드만 있는 익명 클래스는 `lambda expression`으로 대체 할 수 있다. 
{% highlight java %}
processPersons(
    roster, 
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25,
    (Person p) -> p.pritnPerson()
);
{% endhighlight %}

void를 리턴하는 하나의 추상 메서드만 있는 `PrintPerson`와 같은 인터페이스는 이미 정의 되어있는 `Consumer` 인터페이스를 사용하면 된다. 이 인터페이스는 `accept`라는 추상 메서드를 갖는다.
{% highlight java %}
interface Consumer<T> {
    void accept(T t);
}
{% endhighlight %}

따라서 `processPersons`는 `PrintPerson` 인터페이스 대신 `Consumer<Person>`를 파라미터로 입력받으면 된다.
{% highlight java %}
public static void processPersons(
    List<Person> roster,
    Predicate<Person> tester,
    Consumer<Person> block) {
        for (Person p : roster) {
            if (tester.test(p)) {
                block.accept(p);
            }
        }
}
{% endhighlight %}

그리고 `Consumer<Person>`를 구현한 클래스를 넘겨주는 대신, 직접 `lambda expression`을 넘겨주면 깔끔하게 된다.
{% highlight java %}
processPersons(
    roster, 
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25,
    p -> p.printPerson()
);
{% endhighlight %}
  
  
  
만약 각각의 `Person`을 단순히 출력하는 것이 아닌, 무언가를 더 하고 싶다면, 예를들어 더 자세한 정보를 얻어오거나, validation을 하고 싶은 경우 어떻게 해야할까?  
if 조건을 만족하는 `Person`에 대해, `printPerson()`하기 전에 전처리 작업을 하고 싶다면 `Function<T, R>` 인터페이스의 `R apply(T t)`를 이용하면 된다. 
{% highlight java %}
public static void processPersonsWithFunction(
    List<Person> roster,
    Predicate<Person> tester,
    Function<Person, String> mapper,
    Consumer<String> block) {
    for (Person p : roster) {
        if (tester.test(p)) {
            String data = mapper.apply(p);
            block.accept(data);
        }
    }
}
{% endhighlight %}
`Function<Person, String> mapper`를 파라미터로 전달 받고, 내부에선 `mapper`의 `apply` 메서드를 호출해서 전처리 작업을 수행한다.

아래의 사용예는, 특정 조건을 만족하는 `Person`의 email주소값을 얻어와서(전처리작업) 출력하도록 하는 코드이다.
{% highlight java %}
processPersonsWithFunction(
    roster,
    p -> p.getGender() == Person.Sex.MALE
        && p.getAge() >= 18
        && p.getAge() <= 25,
    p -> p.getEmailAddress(),
    email -> System.out.println(email)
);
{% endhighlight %}

### Approach 8: Use Generics More Extensively
이제 제네릭하게 고쳐보자.
모든 타입은 template로 변경하고, `Person` 대신 `Elements`라는 일반적인 명사로 변경한다. 
{% highlight java %}
public static <X, Y> void processElements(
    Iterable<X> source,
    Predicate<X> tester,
    Function <X, Y> mapper,
    Consumer<Y> block) {
    for (X p : source) {
        if (tester.test(p)) {
            Y data = mapper.apply(p);
            block.accept(data);
        }
    }
}
{% endhighlight %}
`X`타입의 순회가능한 source를 입력받아, tester의 조건을 만족하는 `X`에 대해 전처리 작업으로 mapper의 `apply`를 수행하여 얻은 `Y`를 block의 `accept`동작을 수행한다. 

### Approach 9: Use Aggregate Operations That Accept Lambda Expressions as Parameters
`Aggregate Operations`를 이용하여 위의 작업을 구현해보자.
{% highlight java %}
roster
    .stream()
    .filter(
        p -> p.getGender() == Person.Sex.MALE
            && p.getAge() >= 18
            && p.getAge() <= 25)
    .map(p -> p.getEmailAddress())
    .forEach(email -> System.out.println(email));
{% endhighlight %}
`filter`로 특정 조건을 만족하는 원소를 걸러내고, 각각을 `map`으로 전처리 작업을 수행한 다음, `forEach`로 최종 동작을 수행한다.

---
참고자료 : [http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html#approach3](http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html#approach3)