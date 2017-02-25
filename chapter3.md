# 3. String, Comparator, 그리고 filter

### Method Reference, Parameter Routing

책의 소제목은 스트링 이터레이션이라고 되어있지만 해당 장에서의 핵심내용은 Method Reference기능을 이용할 경우, Parameter가 Routing되는 방식이라 위와 같이 소제목을 지었다. Parameter Routing이란 **파라메터를 명시적으로 작성하지 않아도 알아서 함수호출시 매개변수로 전달하는 것**이다.



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

위처럼 메서드레퍼런스 방식을 사용하게되면, 컴파일러가 파라메터라우팅을 어떻게 해야할지 결정해야 한다. 컴파일러는 우선 해당 메서드가 인스턴스 메서드인지, 정적메서드인지 체크한다.

* 인스턴스 메서드일경우
  * 메서드는 파라미터를 호출하는 타깃이 된다. =&gt; parameter.toUpperCase\(\)
* 정적 메서드일경우
  * 파라메터는 메서드의 인수로 라우팅 =&gt; System.out.println\(parameter\)

* **정적메서드, 인스턴스메서드 모두 존재하는 경우에는 컴파일러 오류가 발생한다!**





