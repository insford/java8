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

prices의 합계를 구하고, 20보다 크면 10%를 할인하는 로직

**명령형 스타일**

```java
BigDecimal totalOfDiscountedPrices = BigDecimal.ZERO;

for(BigDecimal price : prices) {
  if(price.compareTo(BigDecimal.valueOf(20)) > 0) 
    totalOfDiscountedPrices = 
      totalOfDiscountedPrices.add(price.multiply(BigDecimal.valueOf(0.9)));
}
System.out.println("Total of discounted prices: " + totalOfDiscountedPrices);

```

**서술적 스타일**

```java
final BigDecimal totalOfDiscountedPrices = 
    prices.stream()
        .filter(price -> price.compareTo(BigDecimal.valueOf(20)) > 0)
        .map(price -> price.multiply(BigDecimal.valueOf(0.9)))
        .reduce(BigDecimal.ZERO, BigDecimal::add);

System.out.println("Total of discounted prices: " + totalOfDiscountedPrices);
```

위의 코드는 기존의 습관적 방법으로 작성한 코드에 비해 상당히 향상되었다.

* 코드가 어수선하지 않고 더 짜임새 있게 구성됐다.
* 로우-레벨 오퍼레이션을 사용하지 않는다.
* 로직을 강화하거나 변경하기에 더 쉽다.
* 메서드를 사용할 때 이터레이션은 라이브러리에 의해 제어된다.
* 더 효율적이다. 루프에 대한 레이지 이벨류에이션\(lazy evaluation\)이 가능하다.
* 원하는 부분을 병렬화 하기가 더 쉽다.

**명령형 프로그래밍의 번거로움을 해결하는 함수형 프로그래밍 방법의 중요한 기능 중 하나가 람다\(Lambda\)이다.**



### 함수형 스타일 코드의 큰 이점

* 변수의 명시적인 변경이나 재할당의 문제를 피할 수 있다. \(immutability\)
* 함수형 버전은 쉽게 병렬화가 가능하다.
* 서술적인 코드작성이 가능하다.
* 함수형 버전은 더 간결하고, 직관적이다. 

### 함수형 스타일로 코딩해야 하는 이유





























