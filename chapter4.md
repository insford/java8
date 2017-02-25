# 4. 람다 표현식을 이용한 설계

자바에서 OOP와 함수형 스타일은 서로 보완관계이며 함께 사용했을 때 상당한 효과를 볼 수 있다. 이 두가지 방법을 사용하면 수정과 확장이 쉽고 유연하며 효율적인 설계를 할 수 있다.

### 람다를 사용한 문제의 분리

클래스를 만드는 이유는 코드를 재사용하기 위해서이다. 이것은 좋은 의도이기는 하나 항상 올바른 것은 아니다. 고차 함수를 사용하면 클래스의 복잡한 구조 없이도 같은 목적을 달성할 수 있다.

##### 설계 문제 살펴보기

문제를 분리하는 설계 아이디어를 설명하는 방법으로 asset 값들을 더하는 예제부터 시작해보자.

Asset Bean

```java
public class Asset {
  public enum AssetType { BOND, STOCK }; 
  private final AssetType type;
  private final int value;
  public Asset(final AssetType assetType, final int assetValue) {
    type = assetType;
    value = assetValue;
  }
  public AssetType getType() { return type; }
  public int getValue() { return value; }
}
```

주어진 asset의 모든 값을 더하는 메서드를 작성해보자.

```java
public static int totalAssetValues(final List<Asset> assets) {
  return assets.stream()
               .mapToInt(Asset::getValue) //primitive type을 지원하기위한 맵
               .sum();
}
```

mapToInt는 primitive type을 지원하기위한 함수이다. IntStream으로 반환되며, IntStream은 sum\(\)이라는 추가적인 기능을 제공한다.

##### 복잡한 문제 다루기

전체의 합계가 아닌 채권\(Bond\), 주식\(Stock\) 각각에 대해서 합계를 구하는 함수를 만들어 보자. \(모든값을 더하는 메서드를 작성하였지만 생략함\)

전체의 합, 채권의 합, 주식의 합을 구하는 메서드는 [Strategy Pattern](http://www.insford.com/wiki/Wiki.jsp?page=%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%26%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4#section-_EA_B0_9D_EC_B2_B4_EC_A7_80_ED_96_A5_26_EB_94_94_EC_9E_90_EC_9D_B8_ED_8C_A8_ED_84_B4-StrategyPattern)을 적용하기에 좋은 경우이다. 자바에서는 이 패턴을 구현하기 위해 종종 인터페이스, 클래스 등을 생성하지만, 람다 표현식으로 적용해보면 아래처럼 구현이 가능하다.

```java
public static int totalAssetValues(final List<Asset> assets,
      final Predicate<Asset> assetSelector) {
  return assets.stream()
               .filter(assetSelector)
               .mapToInt(Asset::getValue)
               .sum();
}

//사용 (assets는 생략)
System.out.println("Total of all assets: " + 
  totalAssetValues(assets, asset -> true));

System.out.println("Total of bonds: " + 
  totalAssetValues(assets, asset -> asset.getType() == AssetType.BOND));

System.out.println("Total of stocks: " + 
  totalAssetValues(assets, asset -> asset.getType() == AssetType.STOCK));
```

Predicate 인터페이스는 참,거짓을 리턴하는 함수형이다.

### 람다 표현식을 사용하여 딜리게이트하기

클래스 레벨에서 문제를 분리하기. 재사용 측면에서 보면 딜리게이트\(delegate\)는 상속보다 더 좋은 설계 도구이다.

##### 딜리게이트 생성

책임져야 할 부분을 다른 클래스에 딜리게이트하는 것보다, 메서드 레퍼런스로 딜리게이트한다.

```java
private Function<String, BigDecimal> priceFinder; //주식시세값을 반환한다.
//주식시세표와, 주식수로 주식의 가치를 결정하는 함수
public BigDecimal computeStockWorth(final String ticker, final int shares) {
  return priceFinder.apply(ticker).multiply(BigDecimal.valueOf(shares));
}
```

위 예제에서는 주식시세값을 반환하는 부분을 딜리게이트하였다. 이부분을 클래스에서 직접 구현하기보다, 외부에서 주입이 가능하도록 변경해보자 \([Dependency inversion principle 원칙](http://www.insford.com/wiki/Wiki.jsp?page=%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%26%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4#section-_EA_B0_9D_EC_B2_B4_EC_A7_80_ED_96_A5_26_EB_94_94_EC_9E_90_EC_9D_B8_ED_8C_A8_ED_84_B4-_EC_9D_98_EC_A1_B4_EA_B4_80_EA_B3_84_EC_97_AD_EC_A0_84_EC_9B_90_EC_B9_99DIPDependencyInversionPrinciple)\) 이렇게 하는 ㅇㅣ유는 코드를 좀 더 확장할 수 있도록 한다.

```java
//생성자
public CalculateNAV(final Function<String, BigDecimal> aPriceFinder) {
  priceFinder = aPriceFinder;
}
```



