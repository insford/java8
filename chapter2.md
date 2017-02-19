# 2. 헬로 람다 표현식

이 장에서는 람다 표현식을 사용하여 컬렉션을 다루는 방법에 대해 알아본다.

### 리스트를 사용한 이터레이션

아래와 같은 코드를 이용하여, 불변\(Immutable\) 컬렉션을 쉽게 생성할 수 있다.

```java
final List<String> friedns = 
    Arrays.asList("Brian", "Jake", "Lion", "Rabbit");
```

기존의 각 엘리먼트를 이터레이션하여 출력하는 방식

```
for (int i = 0; i < friedns.length; i++) {
    System.out.println(friedns.get(i));
}
```

for loop는 본질적으로 순차적인 방식이라 병렬화 하기가 어렵다.

람다표현식을 이용하여 아래와 같이 나타낸다.

```java
//기본적인 람다 표현식 (타입추론은 컴파일러가 처리한다)
friedns.forEach((name) -> System.out.println(name))
//메서드 레퍼런스
friedns.forEach(System.out::println)
```

## 리스트의 변형

List 내부의 값을 변형할 때에도 람다식을 이용하여 변환이 가능하다.

기존의 코드

```java
//friends를 모두 대문자료 변형하여 출력
final List<String> uppercaseNames = new ArrayList<String>();
friends.forEach(name -> uppercaseNames.add(name.toUpperCase()));
System.out.println(uppercaseNames);
```

람다에서는 map 함수를 이용하여 값을 변경할 수 있다.

```java
//람다 표현식
friends.stream()
    .map(name -> name.toUpperCase())
    .forEach(name -> System.out.print(name + " "));     
//메서드 레퍼런스
friends.stream()
    .map(name::toUpperCase)
    .forEach(name -> System.out.print(name + " "));     
```

* stream\(\) 메서드는 jdk8의 모든 컬렉션에서 사용할 수 있으며 스트림 인스턴스에 대한 컬렉션을 래핑한다.
* map 메서드는 입력 엘리먼트 수와 결과 엘리먼트의 수가 동일하다는 것을 보장한다.

### 엘리먼트 찾기

N으로 시작하는 엘리먼트를 선택하는 예제

기존의 코드

```java
final List<String> startsWithN = new ArrayList<String>();
for(String name : friends) {
  if(name.startsWith("N")) {
      startsWithN.add(name);
  }
}
```

람다식 이용

```java
//filter 메서드는 boolean 결과를 리턴하는 람다 표현식이 필요함.
final List<String> startsWithN =
friends.stream()
      .filter(name -> name.startsWith("N"))
      .collect(Collectors.toList()); //Collector는 3장에서 자세히...
```

### 람다 표현식의 재사용성

N으로 시작하는 엘리컨트를 선택하는 람다표현식을 재사용

```java
final Predicate<String> startsWithN = name -> name.startsWith("N");

final long countFriendsStartN = 
  friends.stream()
         .filter(startsWithN)
         .count();
final long countEditorsStartN = 
  editors.stream()
         .filter(startsWithN)
         .count();
final long countComradesStartN = 
  comrades.stream()
          .filter(startsWithN)
          .count();
```

### 렉시컬 스코프와 클로저 사용하기

람다 표현식에서의 중복

```java
final Predicate<String> startsWithN = name -> name.startsWith("N");
final Predicate<String> startsWithB = name -> name.startsWith("B");

final long countFriendsStartN = 
  friends.stream()
         .filter(startsWithN).count();         
final long countFriendsStartB = 
  friends.stream()
         .filter(startsWithB).count();
```

렉시컬 스코프로 중복 제거하기

```java
public static Predicate<String> checkIfStartsWith(final String letter) {
  return name -> name.startsWith(letter);
}
final long countFriendsStartN =
  friends.stream()
         .filter(checkIfStartsWith("N")).count();
final long countFriendsStartB =
  friends.stream()
         .filter(checkIfStartsWith("B")).count();
```

* 변수 letter의 범위는 startsWith 함수의 범위에 있지 않기 때문에 람다표현식의 정의에 대해 범위를 정하고 그 범위 안에서 변수 letter를 찾는다. 이것을 렉시컬 스코프\(lexical scope\)라고 한다. \(함수가 선언된 위치에 따라 정의되는 스코프\)

### 엘리먼트 선택

컬렉션에서 하나의 엘리먼트를 찾아 출력하는 메서드

기존 코드에서의 문제점

```java
String foundName = null;
for(String name : names) {
  if(name.startsWith(startingLetter)) {
    foundName = name;
    break;
  }
}
if(foundName != null) {
  System.out.println(foundName);
} else {
  System.out.println("No name found");
}
```

* 데이터를 찾지 못할경우에 대비하여 null체크를 해야한다.

람다표현식에서 Optional을 이용한 처리

```java
final Optional<String> foundName = 
  names.stream()
       .filter(name ->name.startsWith(startingLetter))
       .findFirst();
//데이터가 존재할경우, 존재하지 않을경우에 대한 처리
System.out.println(foundName.orElse("No name found"));

//ifPresent 이용하여 값이 존재하는 경우만 출력
foundName.ifPresent(name -> System.out.println(name))
```

* Optional클래스는 결과가 없을수도 있는 경우에 유용하다. \(NullPointException이 발생하는 것을 막아준다\)

### 컬렉션을 하나의 값으로 리듀스

리스트에서 엘리먼트의 길이가 가장 긴 항목을 찾고, 그중 첫번째 엘리먼트를 출력하는 예제

```java
//friends가 empty일수도 있어서 Optional이다.
final Optional<String> aLongName = 
  friends.stream()
         .reduce((name1, name2) -> 
            name1.length() >= name2.length() ? name1 : name2);
aLongName.ifPresent(name ->
  System.out.println(String.format("A longest name: %s", name)));
```

* reduce가 parameter로 받는 람다표현식은 BinaryOperator라는 함수형 인터페이스다.

### 엘리먼트 조인

```java
friends.stream()
     .map(String::toUpperCase)
     .collect(joining(", ")));
// A, B, C, D와 같은 형태로 결과가 노출됨. (collect 는 3장에서 자세히 다룸)
```









