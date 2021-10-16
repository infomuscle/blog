# 블로그

> 모든 포스팅은 [티스토리 블로그](https://bortfolio.tistory.com/)에서도 확인하실 수 있습니다.


## 목차

- [알고리즘](./algorithm)
    - [카카오 2019 블라인드 - 오픈채팅방](./algorithm/210303_카카오_2019_블라인드_문제풀이_오픈채팅방)
    - [카카오 2019 블라인드 - 실패율](./algorithm/210307_카카오_2019_블라인드_문제풀이_실패율)
    - [카카오 2019 블라인드 - 후보키](./algorithm/210416_카카오_2019_블라인드_문제풀이_후보키)
    - [카카오 2019 블라인드 - 무지의 먹방 라이브](./algorithm/210422_카카오_2019_블라인드_문제풀이_무지의_먹방_라이브)
- [자바](./java)
    - [JVM Garbage Collector](./java/210501_JVM_Garbage_Collector)
    - [String의 equals()와 hashCode()](./java/210513_String의_equals()와_hashCode())
    - [MyBatis #과 $의 차이](./java/210914_MyBatis_%23과_%24의_차이)
    - [객체지향과 SOLID](./java/211011_객체_지향과_SOLID)
    - [JPA 영속성 컨텍스트](./java/211016_JPA_영속성_컨텍스트)
- [네트워크](./network)
    - [URI vs URL vs URN](./network/211012_URI_vs_URL_vs_URN)
- [스프링](./spring)
    - [스프링부트 어노테이션 정리](./spring/210315_스프링부트_어노테이션_정리)
    - [IntelliJ IDEA에서 Spring Boot 프로젝트 생성](./spring/210504_IntelliJ_IDEA에서_Spring_Boot_프로젝트_생성)
    - [@Transactional 어노테이션의 롤백 테스트](./spring/210512_@Transactional_어노테이션의_롤백_테스트) 
- [파이썬](./python)
    - [파이썬 문자열 함수 정리](./python/210316_파이썬_문자열_함수_정리)
    - [파이썬 버전 및 가상환경 관리 - pyenv](./python/201127_파이썬_버전_및_가상환경_관리_pyenv)
- [데이터베이스](./database)
    - [데이터베이스란](./database/210212_데이터베이스란)
    - [데이터 모델과 관계형 데이터베이스](./database/210304_데이터_모델과_관계형_데이터베이스)
    - [mac OS에서 도커로 오라클 EE 12c 설치](./database/210318_mac_OS에서_도커로_오라클_EE_12c_설치)
    - [mac OS에서 도커로 오라클 XE 11g 설치](./database/210319_mac_OS에서_도커로_오라클_XE_11g_설치)
    - [SELECT로 데이터 조회하기](./database/210325_SELECT로_데이터_조회하기)
- [기타](./etcl)
    - [마크다운 문법 정리](./tool/210305_마크다운_문법_정리)
    - [IntelliJ IDEA 플러그인 추천](./tool/210326_InetlliJ_IDEA_플러그인_추천)
    - [398일간의 1일 1커밋 회고](https://github.com/infomuscle/blog/tree/master/etc/210711_365%EC%9D%BC%EA%B0%84%EC%9D%98_1%EC%9D%BC_1%EC%BB%A4%EB%B0%8B_%ED%9A%8C%EA%B3%A0)




### 작성 예정
- JPA, Hibernate, Spring Data JPA
- OSI 7 Layer
- AOP
- Ioc, DI
- REST API
- 통합 테스트와 단위 테스트
- Oracle 자료형 정리
- 자바 스트림
- Vim 사용법
- 스프링부트 배너 변경
- xor 스왑
- 자료형과 2의 보수
- 로드밸런서
- GC 튜닝
- MyBatis @Alias
- 깃북
- 동시성 이슈와 ConcurrentHashMap
- Jenkins, Docker, 빌드 및 배포 자동화
- Java Static
- 스프링 의존관계 주입 방법 + 롬복
- request 스코프
- 메모리 단편화
- 로드밸런서
- 컴팩션



# 내가 보기 위한 CS 초간단 요약

- 적당히 정리 되면 파일 분리하여 링크 예정

# 자바

## [객체지향과 SOLID](./java/211011_객체_지향과_SOLID)
- 객체지향
    - `추상화`: 현실세계의 개념을 반영하는 것. 객체들의 공통적인 특징, 데이터와 기능을 도출한다.
    - `캡슐화`: 객체의 역할을 수행하기 위한 하나의 목적을 위해 데이터와 기능들을 묶는 것
    - `상속성`: 새로운 클래스가 기존의 클래스의 자료와 연산을 이용하게 해주는 것
    - `다형성`: 하나의 클래스나 메소드를 다양한 방식으로 이용할 수 있게 한다. (오버라이딩,오버로딩)
- SOLID
    - SRP: 단일 책임 원칙(Single Responsibility)
    - OCP: 개방/폐쇄 원칙(Open/Closed)
    - LSP: 리스코프 치환 원칙(Liskov Substitution)
    - ISP 인터페이스 분리 원칙(Interface Segregation)
    - DIP: 의존관계 역전 원칙(Dependency Inversion)

## 바이트코드
> 자바는 JVM을 이용한 플랫폼 독립적이라는 특징이 있습니다. 자바에서 코드를 컴파일하면 바이트코드 즉 (.class)형태로 출력이 되는데 이 Class형태는 JVM에 의해 런타임시완벽한 기계코드로 변경되어 실행됩니다. 따라서 JVM만 있으면 재컴파일 없이 여러 플랫폼에서 똑같이 실행되는 장점이 있습니다. 추가적으로 자바의 장점으로는 포인터 연산이 필요 없고 객체지향이라는 점, 그리고 멀티쓰레딩, 네트워킹 API를 제공한다는 점을 들 수 있습니다. 단점으로는 JVM을 사용하기 때문에 발생하는데요, JVM은 많은 메모리를 사용하고 실행속도 또한 빠르지 않습니다. 또 바이트 코드를 완전한 기계코드로 변환하는 과정에서 일반적인 컴파일 언어보다 속도가 많이 느리다는 단점이 있습니다.

## 자료형

### 자료형이란?

자바에서 다룰 수 있는 데이터의 종류를 말한다. 좀 더 엄밀하게 얘기하면, 데이터를 메모리에 저장하기 위해 공간을 할당할 때 그 공간의 특성을 의미한다.

### 기본 자료형

- 정수형
    - `byte`: 1byte == 8bit == 2^8
    - `short`: 2byte == 16bit == 2^16
    - `int`: 4byte == 32bit == 2^32
    - `long`: 8byte == 64bit = 2^64
- 문자형
    - `char`: 2byte == 16bit == 2^16
- 실수형
    - `float` : 4byte == 32bit == 2^32
    - `double`: 8byte == 64bit == 2^64
- 논리형
    - `boolean`: 1byte == 8bit == 2^8

### 참조 자료형

참조 타입은 값 자체가 아닌 객체의 주소가 저장된다.

- 배열(Array)
- 열거(Enumeration)
- 클래스(Class)
- 인터페이스(Interface)


### 자료형과 변수의 차이는?

- 변수: 메모리를 저장하는 공간
- 자료형: 그 공간의 특성

## 접근제어자
- `public`: 어디서나 접근 가능
- `protected`: 같은 패키지 및 자식 클래스에서 접근 가능
- `default`: 같은 패키지에서만 접근 가능
- `private`: 클래스 내부에서만 접근 가능 

## Stack && Heap

### Stack

- 정적으로 메모리가 할당된다.
- `Primative` 타입의 데이터가 값과 함께 할당된다.
- `Heap`에 생성된 오브젝트의 참조값이 할당된다.
- `Thread`마다 고유의 `Stack`을 가진다.
- 지역변수와 매개변수가 저장된다.(전역변수, 정적변수는 데이터 영역에)
- 높은 주소에서 낮은 주소로

### Heap

- 동적으로 메모리가 할당된다.
- 오브젝트가 저장된다.
- 낮은 주소에서 높은 주소로

> Stack Overflow  
> 스택 프레임이 스택 영역의 경계를 넘어서서 힙 영역과 겹치게 되는 오류

## [JVM 가비지 컬렉터](./java/210501_JVM_Garbage_Collector)

- Stack의 변수는 Heap의 오브젝트를 참조한다. 변수와 오브젝트의 연결이 끊어진 상태를 Unreachable, JVM GC는 이 상태의 오브젝트에 대해 메모리를 해제한다.
- Heap은 Young과 Old로 구분되고, Young은 또 Eden과 Survival들로 구분된다. 각 영역이 찰 때 GC가 발생하고, 살아남은 오브젝트를 다른 영역으로 옮긴다. Young을 Minor GC, Old를 Major GC라고 부른다.
- 오브젝트는 처음 Eden에 생성. Eden에서 살아남으면 Survival로 이동. Survival에서 살아남은 건 다른 Survival로 이동. 왔다갔다 하다가 특정 age에 도달하면 Old로 이동.
- GC 전략에는 무엇이 있을까?


## [String의 equals()와 hashCode()](./java/210513_String의_equals()와_hashCode())

- 리터럴과 new의 차이
- String Constant Pool
- hashCode

## 정리 필요
- 추상 클래스(abstract class)와 인터페이스(interface)..
- 컬렉션(collection) 클래스에서 제네릭을 사용하는 이유.
- 래퍼 클래스(wrapper class)

# 스프링
- MVC 패턴
    - `Controller`: 사용자로부터 요청을 받는 영역
    - `Model`: 데이터를 처리하는 영역
    - `View`: 처리 결과를 바탕으로 응답을 표시하는 영역

- IoC, DI

# 네트워크

## OSI 7 Layer

- `Physical Layer`: 비트를 전자기파로 바꾸는 역할. PHY였나? 물리적으로 구현되어 있다.
- `Data Link Layer`: 내용 앞뒤로 구분자(?)를 추가하는 역할. Lan 카드인가 물리적으로 구현되어 있다.
- `Network Layer`: 주소 추가.
- `Transport Layer`: 포트 추가.
- `Application Layer`: 현대에 5~7은 Application Layer로 통합.
- 5와 6은 무엇을 할까? 

## HTTP 프로토콜
- `Hyper Text Transfer Protocol`
- 구성
    - Header
    - Body

## Restful API
- 아키텍처
- `Representational State Transfer`
- HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시.
- HTTP Method와 해당 자원에 대한 CRUD Operation 매핑
    - `POST`: Create
    - `GET`: Read
    - `PUT`: Update
    - `DELETE`: Delete
- 장점
    - 의도하는 바를 쉽게 파악할 수 있다.
- 단점
    - 표준이 없다.

##  HTTP vs HTTPS

## TCP vs UDP
- `TCP`: 상호작용. 계속 확인함. 신뢰성 중요.
- `UDP`: 보내고 끝. 빠름. 효율성 중요.


# 데이터베이스

## Table 인덱스 장점

## 트랜잭션

- 데이터베이스의 기능을 논리적 작업으로서 하나의 단위로 묶은 집합
    - (논리적) 이체
    - (기능적) UPDATE 출금 계좌 && UPDATE 입금 계좌

### ACID
- Atomicity(원자성): 커밋 || 롤백 -> 집합의 각 기능은 모두 반영되거나, 아무것도 반영되지 않아야 한다.
- Consistency(일관성): 작업 처리 결과는 항상 일관적이어야 한다.
- Isolation(독립성): 여러 트랜잭션이 실행 중일 때 다른 트랜잭션에 간섭할 수 없다. -> 데이터베이스 락
- Durability(지속성): 트랜잭션 커밋 결과는 영구 반영되어야 한다.

### 데이터베이스 락

- ex) 100,000원 계좌에서 10,000원 출금 요청 A와 10,000원 출금 요청 B가 0.1초 간격으로 들어온다면?
        1. A가 현재 잔액 100,000원 조회
        2. B가 현재 잔액 100,000원 조회
        3. A가 10,000원 뺀 90,000원 업데이트
        4. B가 10,000원 뺀 90,000원 업데이트
        5. 남은 잔액은 90,000원? -> 이럼 안된다.

### 트랜잭션 격리 수준

데이터 정합성과 동시성의 trade-off 때문에 현실적으로는 격리 수준을 고려한다.

- Read uncommited
- Read commited
    - Problem: Unrepeatable read
- Repeatable read
    - Problem: Phantom read
- Serializable

# 운영체제

## 메모리 구조

|영역|설명|
|---|---|
|코드||
|데이터||
|힙||
|스택||

## 스케줄링
- Round Robin

## 동적할당

## 프로세스 vs 쓰레드

# 소프트웨어 공학

## SOLID

# 기타

## MyBatis vs JPA

##  참고
- https://martianlee.github.io/posts/naver-interview-review/
- https://remover.tistory.com/177
- https://jw910911.tistory.com/8
- https://dongza.tistory.com/m/13
- https://github.com/JaeYeopHan/Interview_Question_for_Beginner
- https://github.com/MaximAbramchuck/awesome-interview-questions
- https://mellowp-dev.tistory.com/4