# 1. 헬로 람다 표현식

### 사고의 전환

##### 함수형 스타일에 대해 간단히 알아보기

명령형\(Imperative\) 스타일에서 서술적\(Declarative\) 스타일로의 전환

**명령형 스타일**

```java
for (String city : cities) {
    if (city.equals("Chicago")) {
        found = true;
        break;
    }
}
System.out.println("Found chicago? : " + found)
```

**개선된 서술적 스타일의 예**

```java
System.out.println("Found chicago? : " + cities.contains("Chicago"))
```

위와 같이 코드를 수정하면 이전 코드에비해 더 나은 코드가 될 수 있다.

* 난잡한 가변변수\(Mutable Variable\)의 사용을 방지
* 이터레이션에 대한 코드가 외부로 드러나지 않기 때문에 개발자는 이터레이션 자체 코드에 대해서 신경 쓸 필요가 없음
* 어수선한 코드의 사용을 막아줌
* 코드에 대한 설명이 명확해짐
* 비즈니스 의도는 유지하면서 코드는 명료해짐
* 오류의 발생확률을 줄여줌
* 이해하고 쉽고 유지보수가 쉬움



##### 조금 더 복잡한 케이스











