# 1. 헬로 람다 표현식

## 사고의 전환

**함수형 스타일에 대해 간단히 알아보기**

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

**조금 더 복잡한 케이스**

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

## 함수형 스타일 코드의 큰 이점

* 변수의 명시적인 변경이나 재할당의 문제를 피할 수 있다. \(immutability\)
* 함수형 버전은 쉽게 병렬화가 가능하다.
* 서술적인 코드작성이 가능하다.
* 함수형 버전은 더 간결하고, 직관적이다. 

## 함수형 스타일로 코딩해야 하는 이유

**문제의 핵심은 이터레이션**

기존에는 컬렉션에 있는 각 엘리먼트를 반복하고 출력할 때, for loop를 사용하고, 몇개의 추가 가변변수 오퍼레이션을 지원하기 위해 loop안에 코드를 추가해야했다.

현재 자바에서는 다양한 오퍼레이션을 위해 특별한 내부 이터레이터를 제공한다. loop를 간단하게 하고, 데이터값을 매핑, 선택한 값을 필터링, 감소\(reduce\)시키며 최소, 최대 평균값을 구하는 편리한 기능을 제공하기도 한다. \(2장에서 다시 설명\)

**개발할 때 고려해야 하는 정책의 강화**

오퍼레이션이 보안인증을 받아야 한다는 것을 보장하는 경우에 대한 기존코드

```java
Transaction transaction = getFromTransactionFactory();

// ...트랜잭션 안에서 실행하는 오퍼레이션...

checkProgressAndCommitOrRollbackTransaction();
UpdateAnditTrail();
```

람다함수를 이용한 코드

```java
runWithinTransaction((Transaction transanction) -> {
// ...트랜잭션 안에서 실행하는 오퍼레이션...
});
```

* 상태를 체크하는 정책과 감시기록을 업데이트하는 작업은 runWithinTransaction\(\) 메서드 안에서 추상화되고 캡슐화된다.
* try-catch문을 추가하더라도, runWithinTransaction\(\) 메서드 안에서 처리가 가능하다.

**정책 확장하기**

확장성의 기본 구조는 하나 이상의 인터페이스로 되어있다는 점이다. 이러한 형태는 클래스의 계층구조를 주의 깊게 설계해야 하고, 복잡해지며 향후 유지보수와 확장이 어려워진다.

그에비해 함수형 인터페이스와 람다표현식을 사용하면 확장할 수 있는 정책을 설계하도록 도와준다. 추가적인 인터페이스를 작성하지 않고, 코드의 비헤이비어\(Behavior\)에 집중해야한다.

**손쉬운 병렬화.**

람다를 이용하면 순차방식과 병렬방식의 코드사이에 차이점이 없게 되므로, 손쉽게 병렬화가 가능하다.

**스토리 텔링**

비즈니스 로직과 코드를 유사하게 가져갈 수 있어, 유지보수비용이 감소한다.

```java
//모든 주식시세에 대한 가격을 구하고, $500 이하의 주식가격을 찾아서 그 주식의 가격을 합산하라.
tickers.map(StockUtil::getPrice).filter(StockUtil::priceIsLessThan500).sum()
```

**문제의 분리**

조건에 따라 로직을 분리하려할 때, 기존 OOP에서는 전략패턴\(Strategy Pattern\)을 사용하여  해결하지만, 람다를 사용하여 더 적은 코드로 구현이 가능하다.

**지연연산**

몇 개의 오퍼레이션을 피하거나 적어도 잠시 실행을 연기시키는 것은 성능을 향상시킬 수 있는 가장 쉬운 방법이며, 해당기능을 손쉽게 제공한다.

## 혁명이 아닌 진화

자바 언어팀은 언어와 JDK에 함수형 기능을 넣기 위해 많은 시간과 노력을 쏟아왔다. 몇개의 가이드라인을 따르면 우리의 코드를 향상시킬 수 있다.

**서술적\(Be Declarative\)**

첫 예제에서 봤듯이, 불변 컬렉션\(Immutable Collection\)에서 contains\(\)메서드를 서술적으로 사용하는 것이 명령적 스타일을 사용하는 경우보다 코드를 작성하는 면에서 얼마나 쉬운지 알아봤고, java8에서부터 이러한 기능들을 사용할 수 있게 됐다.

**불변성의 증진\(Promote Immutability\)**

다중 변수의 변경이 있는 코드는 이해하기 어렵고 병렬화하기가 상당히 까다롭다. 불변성은 근본적으로 이런 문제를 제거한다. 자바에서는 불변성을 지원하지만 강제하지는 않는다. 가능한 불변객체를 사용하도록 습관을 바꾸자.

**사이드 이펙트의 회피**

사이드 아펙트가 없는 함수는 불변\(immutability\)이며 함수를 사용하는 중에 함수에 대한 입력의 변경이 없다. 이 함수들은 이해하기가 쉽고 오류의 발생 가능성이 낮으며 더 쉽게 최적화 할 수 있다. \(레이스컨디션, 동기화업데이트 문제를 해결해준다\)

**문법보다는 표현에 주력**

for문을 사용하는 것보단, map\(\), sum\(\)등의 서술문을 이용하는 것이 코드를 간결하게 만들고, 더욱 이해하기 쉽다.

**고차 함수를 사용한 설계**

고차함수\(high order function\) : 함수를 파라미터로 받거나, 함수를 리턴하는 함수.





