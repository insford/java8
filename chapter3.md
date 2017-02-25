# 3. String, Comparator, 그리고 filter

### Method Reference

Method Reference란 **람다표현식에서 메서드호출 1회로 끝나는 코드를 손쉽게 작성할 수 있게 해주는 문법**이다.

람다식을 이용한 스트링 이터레이션

```java
List<String> list = Arrays.asList("a", "b", "c");

list.chars()
  .map(x -> x.toUpperCase())
  .forEach(x -> System.out.println(x));
```

메서드레퍼런스를 이용하면 아래와 같은 방식으로 사용하면 훨씬 더 깔끔하게 코딩이 가능하다.

```java
list.chars()
  .map(String::toUpperCase)
  .forEach(System.out::println);
```

위처럼 메서드레퍼런스 방식을 사용하게되면, 컴파일러가 파라메터 라우팅\(Parameter Routing\)을 어떻게 해야할지 결정해야 한다. 컴파일러는 우선 해당 메서드가 인스턴스 메서드인지, 정적메서드인지 체크한다.

* 인스턴스 메서드일경우
  * 메서드는 파라미터를 호출하는 타깃이 된다. =&gt; parameter.toUpperCase\(\)
* 정적 메서드일경우

  * 파라메터는 메서드의 인수로 라우팅 =&gt; System.out.println\(parameter\)

* **정적메서드, 인스턴스메서드 모두 존재하는 경우에는 컴파일러 오류가 발생한다!**

### Comparator 인터페이스의 구현

해당 인터페이스에서는 jdk내에서도 다양하게 사용된다, 해당 인터페이스는 java8부터 함수형 인터페이스로 바뀌었다. comparator를 구현하기위한 다양한 기법들을 사용하면 많은 이점을 얻을 수 있다.

기본적인 comparator 구현 \(사람을 나이기준으로 정렬한다\)

```java
people.stream()
      .sorted((person1, person2) -> person1.ageDifference(person2))
      .collect(toList());
```

collect\(\)메서드는 이터레이션의 타깃 멤버를 원하는 타입의 포맷으로 변환하는 리듀서\(reducer\)이다. toList\(\)는 Collectors 컨비니언스 클래스의 정적메서드이다.

위 코드는 메서드레퍼런스를 이용하여 아래처럼도 작성이 가능하다.

```java
people.stream()
      .sorted(Person::ageDifference)
      .collect(toList());
```



### Comparator의 재사용

내림차순정렬을 구현할 때, 미리 만들어둔 오름차순을 재활용







