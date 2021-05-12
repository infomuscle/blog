# [Java] String 데이터 비교


## String 생성 방법

1. 리터럴
```java
String a = "12345;
```

2. new 연산자
```java
String b = new String("12345);
```

## 리터럴과 new 연산자 방식의 차이
- 리터럴로 생성된 객체는 `String Constant Pool` 영역에 존재
    - 리터럴은 내부적으로 String의 `intern()` 메소드를 호출.
    - `intern()`은 해당 문자열이 `String Constant Pool`에서 검색 후 주소값 반환 또는 생성
- new 연산자로 생성된 객체는 `Heap` 영역에 존재한다.

## equals()와 ==의 차이
- `.equals()`는 값 비교, `==`은 주소값을 비교한다.

## hashCode
- 각 객체의 주소값을 변환하여 생성한 고유의 주소값
- .equals()를 이용해서 두 객체가 같은지 판단하기 위해서는 .hashCode()가 동일한지 비교한다.