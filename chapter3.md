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

내림차순정렬을 구현할 때, 미리 만들어둔 오름차순으로 생성이 가능하다.

```java
Comparator<Person> compareAscending = 
  (person1, person2) -> person1.ageDifference(person2);
Comparator<Person> compareDescending = compareAscending.reversed();
//이번에 default method로 추가된 컨비니언스 메서드이다
```

### 여러가지 비교연산

Comparator 인터페이스에 추가된 새로운 컨비니언스 메서드에 대해 알아보자.

comparing 메서드,  `(person1.getName().compareTo(person2.getName()))` 와 같은 형식으로 작성해야할 코드를 손쉽게 해준다.

```java
final Function<Person, String> byName = person -> person.getName();
people.stream()
      .sorted(comparing(byName));
```

comparing을 이용한 2차정렬 구현

```java
final Function<Person, Integer> byAge = person -> person.getAge();
final Function<Person, String> byTheirName = person -> person.getName();

people.stream()
      .sorted(comparing(byAge).thenComparing(byTheirName))
      .collect(toList()));
```

thenComparing은 두개의 Comparator를 합성\(composite\)해서 새로운 Comparator를 생성한다. \(아래 코드 참고 : Comparator의 default 메서드로 구현되어있음\)

```java
default Comparator<T> thenComparing(Comparator<? super T> other) {
    Objects.requireNonNull(other);
    return (Comparator<T> & Serializable) (c1, c2) -> {
        int res = compare(c1, c2);
        return (res != 0) ? res : other.compare(c1, c2);
    };
}
```

### collect 메서드와 Collectors 클래스 사용하기

기존에 이미 collect\(\)메서드를 사용했다, 이 메서드는 컬렉션을 다른 형태, 즉 가변 컬렉션\(mutable collection\)으로 변경하는 데 유용한 리듀스\(reduce\) 오퍼레이션이다.

필터링한 결과를 List로 합치고 싶을 경우

```java
List<Person> olderThan20 = new ArrayList<>();
people.stream()
      .filter(person -> person.getAge() > 20)
      .forEach(person -> olderThan20.add(person));
```

forEach를 사용하면 기존 for문을 사용했을때랑 별반 다를 것이 없다. collect 메서드를 이용하면 아래와 같이 서술적으로 구현이 가능하다.

```java
List<Person> olderThan20 = 
  people.stream()
        .filter(person -> person.getAge() > 20)
        .collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
```

collect 메서드는 3가지의 파라메터를 받는다. 아래와 같은 메서드로 라이브러리에서 병렬로 합치는 것도 가능하게 해준다!

* supplier\(결과컨테이너를 만드는 방법\) =&gt; ArrayList::new
* acumulator\(하나의 엘리먼트를 결과 컨테이너에 추가하는 방법\) =&gt; ArrayList:add
* combiner\(하나의 결과 컨테이너를 다른 결과와 합치는 방법\) =&gt; ArrayList::addAll

Collectors 유틸리티를 이용하면, collect에 필요한 대부분의 기능들을 미리 만들어놨다. 위에서 본 코드는 아래처럼 변경이 가능하다!

```java
List<Person> olderThan20 = 
  people.stream()
        .filter(person -> person.getAge() > 20)
        .collect(Collectors.toList());
```

데이터를 그룹으로 묶는 groupingBy\(\)

```java
Map<Integer, List<Person>> peopleByAge = 
  people.stream()
        .collect(Collectors.groupingBy(Person::getAge));
//{35=[a - 35], 20=[b - 20], 30=[c - 30, d - 30, e - 30]}

//mapping 파라메터를 추가하면 결과를 다른형태로
```

mapping기능을 이용해 결과를 다른 형태로도 수집이 가능하다.

```java
Map<Integer, List<String>> nameOfPeopleByAge = 
  people.stream()
        .collect(
          groupingBy(Person::getAge, mapping(Person::getName, toList())));
//{35=[a], 20=[b], 30=[c, d, e]}
```

Collectors에는 위와 같은 기능 외에도 여러가지 메서드가 존재한다. \(toSet\(\), toMap\(\), joining\(\), minBy\(\), maxBy\(\), groupingBy\(\) ...\) 자세한 내용은 API문서를 확인해보자\([https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)\)

### 디렉터리에서 모든 파일 리스트하기

java8에서는 CloseableStream 인터페이스를 이용해서, 파일을 읽는 기능을 손쉽게 만들어준다.

```java
Files.list(Paths.get("."))
   .filter(Files::isDirectory)
   .forEach(System.out::println);
```

### flatMap을 사용하여 서브 디렉터리 리스트하기

주어진 디렉터리에서 서브 디렉터리를 탐색하는 로직을 flatMap기능을 이용하여 구현해보자.

```java
List<File> files = new ArrayList<>();

File[] filesInCurrentDir = new File(".").listFiles();
for(File file : filesInCurrentDir) {
  File[] filesInSubDir = file.listFiles();
  if(filesInSubDir != null) {
    files.addAll(Arrays.asList(filesInSubDir));
  } else {
    files.add(file);
  }
}
```

```java





List<File> files = 
  Stream.of(new File(".").listFiles())
        .flatMap(file -> file.listFiles() == null ? 
            Stream.of(file) : Stream.of(file.listFiles()))
        .collect(toList());
```



