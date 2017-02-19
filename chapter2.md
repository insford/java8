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







